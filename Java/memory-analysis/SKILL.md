---
name: memory-analysis
description: Diagnose Java memory problems including heap leaks, OutOfMemoryError, native memory growth, high allocation rates, and heap dump analysis with MAT and JFR. Use this skill when investigating memory leaks, OOM errors, container OOMKills, growing RSS, or analyzing heap dumps.
---

# memory-analysis

Diagnose Java memory problems including heap leaks, OutOfMemoryError, native memory growth, high allocation rates, and heap dump analysis with MAT and JFR.

## Goal

Turn "memory keeps growing" into a named leaking object graph with a specific GC root holding it — then fix the reference, not the symptom.

---

# When to Use

Use this skill whenever:

- Investigating `OutOfMemoryError` or container OOMKills
- Heap or RSS grows steadily over time
- Analyzing heap dumps
- GC runs constantly with little memory reclaimed
- Sizing memory for a Java service

---

# Step 1: Classify the Problem

The error/symptom tells you where to look:

| Symptom | Meaning |
|---|---|
| `OOM: Java heap space` | Heap exhausted: leak or undersized heap |
| `OOM: Metaspace` | Class/classloader leak (redeploys, dynamic proxies/generated classes) |
| `OOM: unable to create native thread` | Thread leak or OS limits |
| `OOM: Direct buffer memory` | Direct ByteBuffer leak (Netty, NIO) |
| Container killed, exit 137, no Java error | OS OOM killer: total native footprint exceeded container limit |
| Heap sawtooth returning to same baseline | Healthy — not a leak |
| Sawtooth with rising floor | Leak: the post-GC baseline is the signal |

Watch POST-FULL-GC old gen usage over hours/days. Rising floor = leak. Always capture the trend before dump analysis.

---

# Step 2: Capture Evidence

- Automatic dump on OOM (set everywhere in production, costs nothing until needed):
  `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/dumps`
- Manual dump: `jcmd <pid> GC.heap_dump /tmp/heap.hprof` (note: `jmap -dump:live` triggers full GC first — deliberately choose live vs all)
- Two dumps beat one: baseline + after growth; compare to see WHAT grew.
- Quick triage without a dump: `jcmd <pid> GC.class_histogram` twice, diff the top classes.
- JFR: `OldObjectSample` events track long-lived object allocation sites with low overhead — often finds the leak without a dump.

---

# Step 3: Analyze the Dump

Eclipse MAT workflow:

1. Leak Suspects report — right roughly 80% of the time
2. Dominator tree — who RETAINS the memory (retained heap = what would be freed)
3. Path to GC roots (exclude weak/soft refs) on the biggest dominator — this names the culprit reference chain
4. In the two-dump comparison: focus on the classes whose retained size grew

Retained vs shallow: a 48-byte HashMap entry retaining 2 GB is your leak; sort by retained.

---

# Classic Leak Patterns

- Unbounded caches/maps: memoization without eviction — fix with Caffeine (size + expiry), not WeakHashMap guesswork
- Static collections that only ever grow (registries, listener lists without deregistration)
- ThreadLocals not removed on pooled threads (app servers, executors) — values live as long as the thread
- Listeners/callbacks registered but never unregistered (the GC root is the event source)
- Classloader leaks on redeploy: a single thread/TL/static reference into the old classloader retains every class — metaspace OOM
- Unclosed resources: streams, connections, native handles (use try-with-resources; leaked direct buffers show as native growth)
- Hibernate/JPA: session-scoped caches accumulating in long-lived sessions; huge unbounded query results
- Subtle: `subList`/`substring`-style views or interned data pinning large parents

---

# Native / Off-Heap Growth (RSS grows, heap fine)

1. Enable NMT: `-XX:NativeMemoryTracking=summary`, then `jcmd <pid> VM.native_memory summary.diff` against a baseline
2. Check categories: Thread (stack count × size — thread leak?), Class (metaspace), Internal/Other (direct buffers)
3. Direct buffers: monitor `BufferPool` MXBean; cap with `-XX:MaxDirectMemorySize`
4. Beyond NMT (allocator behavior, JNI leaks): jemalloc/tcmalloc profiling; consider glibc arena tuning (`MALLOC_ARENA_MAX`) for high-thread apps
5. Container sizing rule: limit must cover heap + metaspace + threads + direct + code cache + allocator overhead — heap at 50–75% of the container, not 100%

---

# High Allocation Rate (not a leak, but GC pain)

- Profile allocations (async-profiler `-e alloc`, JFR allocation events)
- Usual suspects: per-request JSON/serialization garbage, string concatenation in loops, boxing in hot paths, oversized temporary collections, logging of large objects at debug level
- Fix the top allocation sites; ignore micro-noise

---

# Output Format

## Symptom

Error type, growth trend, timeframe.

## Evidence

Dump/NMT/JFR findings: leaking class, retained size, path to GC root.

## Root Cause

The reference chain and why it accumulates.

## Fix

The code change; plus eviction/lifecycle policy preventing recurrence.

## Verification

Post-fix baseline trend confirming stability.
