---
name: concurrency-review
description: Review Java concurrent code for race conditions, deadlocks, memory visibility, thread pool configuration, and correct use of java.util.concurrent primitives. Use this skill when reviewing or writing multithreaded Java code, executors, CompletableFuture chains, synchronized blocks, or diagnosing race conditions and deadlocks.
---

# concurrency-review

Review Java concurrent code for race conditions, deadlocks, memory visibility, thread pool configuration, and correct use of java.util.concurrent primitives.

## Goal

Find the concurrency bugs that tests rarely catch: races, visibility failures, deadlocks, and pool misconfigurations that only appear under production load.

---

# When to Use

Use this skill whenever:

- Reviewing code with threads, executors, or shared mutable state
- Writing or reviewing `CompletableFuture`/async pipelines
- Configuring thread pools
- Diagnosing suspected races, deadlocks, or "works locally, corrupts under load"
- Reviewing caches, lazy initialization, or singletons

---

# Review Method

For every piece of state, ask:

1. Which threads touch it?
2. Is it mutable after publication?
3. What guarantees ordering and visibility (happens-before)?

If the answer to 3 is "nothing", it's a bug even if it "works" — the JMM permits stale reads and reordering that appear only on other hardware or under load.

Hierarchy of preference:

1. Immutable (records, final fields) — no synchronization needed
2. Confined (thread-local, single-owner) — no sharing
3. Concurrent library types (`ConcurrentHashMap`, `AtomicLong`, queues)
4. Explicit locking — last resort, smallest scope

---

# Race Conditions

Check-then-act and read-modify-write on shared state:

```java
// BROKEN even with ConcurrentHashMap: two operations, no atomicity
if (!map.containsKey(k)) map.put(k, create());   // → map.computeIfAbsent(k, ...)

// BROKEN: ++ is read-modify-write
counter++;                                        // → AtomicLong / LongAdder
```

- A thread-safe collection does NOT make compound operations atomic. Look for any if-then sequence over shared state.
- `volatile` gives visibility, NOT atomicity — fine for flags, wrong for counters.
- Unprotected lazy init: use holder idiom, enum, or plain eager init; double-checked locking requires `volatile`.
- `SimpleDateFormat` and other stateful "utilities" shared across threads: replace with `java.time` types (immutable).

---

# Visibility and Safe Publication

- Sharing a non-final, non-volatile field across threads without a lock = readers may see stale or half-constructed values.
- Safe publication: `final` fields in constructors, `volatile`, locks, or handing objects through concurrent collections/executors.
- Leaking `this` from a constructor (registering listeners, starting threads in constructors) publishes a half-built object.

---

# Deadlocks

- Multiple locks acquired in different orders across code paths = deadlock waiting to happen. Enforce a global lock ordering.
- Holding a lock while calling foreign code (callbacks, listeners, unknown implementations) — the callee may block or take another lock.
- Blocking calls (`.get()`, `.join()`) inside a task running ON the same bounded pool the awaited task needs = thread-starvation deadlock. Classic with `CompletableFuture` on the common pool and with size-limited executors.
- Diagnose live: thread dump (`jcmd <pid> Thread.print`) reports found deadlocks explicitly.

---

# Thread Pools

- `Executors.newCachedThreadPool` (unbounded threads) and `newFixedThreadPool` (unbounded queue) both hide overload failure modes — prefer explicit `ThreadPoolExecutor` with bounded queue and named threads.
- Sizing: CPU-bound ≈ cores; I/O-bound ≈ cores × (1 + wait/compute) — or use virtual threads for I/O-bound (see virtual-threads skill).
- Always: named thread factories (debuggability), rejection policy chosen deliberately, uncaught exception handler.
- Tasks swallowing exceptions die silently — `submit()` traps exceptions in the returned Future; if nobody calls `get()`, nobody knows. Log in the task or use `execute()` with a handler.
- Shutdown: `shutdown()` + `awaitTermination` on application stop; leaked pools prevent JVM exit and leak threads on redeploys.

---

# CompletableFuture

- Default async executor is the shared commonPool — blocking I/O on it starves everything (including parallel streams). Pass an explicit executor for I/O stages.
- Every chain needs `exceptionally`/`handle`/`whenComplete` — dropped exceptional futures vanish silently.
- `thenApply` (same thread) vs `thenApplyAsync` (pool) — mixing them accidentally moves heavy work onto callers.
- Timeouts: `orTimeout`/`completeOnTimeout`; a future that never completes leaks the whole chain.

---

# Common Review Findings

- Compound operations on concurrent collections
- `volatile` used for counters
- HashMap accessed concurrently (infinite loops/corruption pre-resize)
- Locks held during I/O or remote calls
- Unbounded queues masking overload until OOM
- `.join()` inside the pool running the joined task
- Futures without error handling
- Missing pool shutdown hooks

---

# Testing Concurrency

- Deterministic where possible: extract logic so races are testable via interleaving control (CountDownLatch choreography).
- jcstress for genuine memory-model claims; stress tests with many threads catch only the loudest races.
- A passing test does NOT prove thread safety — review reasoning (happens-before analysis) is the real gate.
