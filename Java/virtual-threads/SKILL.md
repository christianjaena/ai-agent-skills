---
name: virtual-threads
description: Adopt and review Java virtual threads (Project Loom) covering when to use them, pinning pitfalls, structured concurrency, migration from thread pools and reactive code, and Spring Boot integration. Use this skill when adopting virtual threads, reviewing Thread.ofVirtual or executor changes, or diagnosing virtual thread pinning and scalability issues.
---

# virtual-threads

Adopt and review Java virtual threads (Project Loom) covering when to use them, pinning pitfalls, structured concurrency, migration from thread pools and reactive code, and Spring Boot integration.

## Goal

Use virtual threads to get reactive-level scalability with simple blocking code — while avoiding the pinning, pooling, and thread-local traps that silently negate the benefit.

---

# When to Use

Use this skill whenever:

- Migrating a service to virtual threads
- Reviewing code using `Thread.ofVirtual`, `Executors.newVirtualThreadPerTaskExecutor`, or structured concurrency
- Deciding between virtual threads and reactive/async styles
- Diagnosing scalability problems or pinning in a virtual-thread app

---

# The Model

- Virtual threads are cheap (thousands to millions), scheduled by the JVM onto a small pool of carrier (platform) threads.
- A blocking call on a virtual thread unmounts it from the carrier — blocking becomes nearly free.
- Therefore: write straightforward blocking code, one virtual thread per task/request.

When they help: I/O-bound concurrency (HTTP calls, DB queries, queues) with many concurrent tasks.

When they DON'T help: CPU-bound work (same cores either way — use a sized platform pool), or apps with modest concurrency that were never thread-starved.

---

# Cardinal Rules

1. Never pool virtual threads. Creation is the point — `newVirtualThreadPerTaskExecutor`, not a fixed pool of them. Pooling reintroduces the bottleneck.
2. Replace semantics, not syntax: rate-limit with `Semaphore`, not by capping thread counts.
3. Virtual threads must not run CPU-heavy loops — they hog carriers; dispatch CPU work to a bounded platform pool.

---

# Pinning

A virtual thread gets pinned (cannot unmount, blocks its carrier) when blocking:

- Inside a `synchronized` block/method (fixed in JDK 24 via JEP 491 — on 21–23 this is the top issue)
- During native calls (JNI)

At scale, pinning under load = carrier starvation = the "virtual threads made nothing faster" outcome.

Actions:

- Detect: `-Djdk.tracePinnedThreads=full` (or JFR `jdk.VirtualThreadPinned` event)
- Fix: replace `synchronized` around blocking calls with `ReentrantLock`
- Audit dependencies: older connection pools, drivers, and clients with `synchronized` internals (upgrade — most major libraries have shipped fixes)

---

# ThreadLocals and Context

- ThreadLocals WORK on virtual threads, but per-thread caching patterns (pooled buffers in TLs) multiply memory by a million threads — remove them.
- Prefer Scoped Values (JEP 506) for immutable context propagation in new code.
- MDC/tracing context: verify your logging/tracing libraries propagate context to virtual threads correctly.

---

# Structured Concurrency

For fan-out within a request, prefer `StructuredTaskScope` over raw fire-and-forget:

```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    var user = scope.fork(() -> userService.find(id));
    var orders = scope.fork(() -> orderService.findFor(id));
    scope.join().throwIfFailed();
    return new Profile(user.get(), orders.get());
}
```

Children can't outlive the scope, failures cancel siblings, and cancellation propagates — eliminating leaked tasks. (Preview API — check status on your JDK.)

---

# Migration Guide

From thread pools:

- Swap request/task executors to virtual-thread-per-task; keep a bounded platform pool for CPU stages
- Convert thread-count throttles to `Semaphore` permits
- Downstream protection now explicit: connection pools and rate limiters are the new bottleneck guardrails (10k virtual threads will happily open 10k DB connection requests)

From reactive (WebFlux/Rx):

- Virtual threads + blocking style now covers most scalability needs with far simpler code and stack traces
- Reactive still wins for streaming/backpressure-centric domains
- Migrate incrementally at service boundaries; don't mix models within one flow

Spring Boot 3.2+: `spring.threads.virtual.enabled=true` switches Tomcat request handling and async executors.

---

# Common Review Findings

- `newFixedThreadPool` of virtual threads (pooling — defeats the purpose)
- `synchronized` around I/O on JDK < 24 (pinning)
- Unbounded fan-out with no semaphore against a fragile downstream
- CPU-bound loops on virtual threads
- ThreadLocal buffer caches carried over from platform-thread designs
- Claiming latency wins: virtual threads improve THROUGHPUT/scalability under concurrency, not single-request latency

---

# Verification

- Load test before/after with realistic concurrency; watch carrier pool utilization and pinning events (JFR)
- Confirm downstream pools/limits hold under the new concurrency ceiling
- Thread dumps: `jcmd <pid> Thread.dump_to_file -format=json` includes virtual threads (classic dumps don't)
