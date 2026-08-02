---
name: jvm-performance
description: Diagnose and tune JVM performance covering GC selection and tuning, heap sizing, JIT behavior, profiling with JFR and async-profiler, and container-aware settings. Use this skill when a Java service has high latency, GC pauses, high CPU, or when sizing and tuning JVM flags for production.
---

# jvm-performance

Diagnose and tune JVM performance covering GC selection and tuning, heap sizing, JIT behavior, profiling with JFR and async-profiler, and container-aware settings.

## Goal

Fix JVM performance problems with profiler evidence and targeted changes — not flag cargo-culting.

---

# When to Use

Use this skill whenever:

- A Java service shows high latency, GC pauses, or CPU saturation
- Sizing heap and choosing a GC for a new service
- Reading GC logs, JFR recordings, or profiler flame graphs
- Reviewing JVM flags in deployment configs

---

# Method: Measure First

1. Define the symptom numerically (p99 latency, CPU %, pause times, throughput).
2. Identify the bottleneck class: CPU-bound, GC-bound, lock contention, or waiting on I/O.
3. Profile, don't guess.

Fast triage:

- High CPU + low GC time → application code: CPU profile (async-profiler, JFR)
- Latency spikes correlating with GC log pauses → GC-bound: tune GC/heap/allocation
- Low CPU + high latency → waiting: thread dumps, lock profiling (`async-profiler -e lock`), downstream latency
- Rising memory + eventual OOM → leak: see memory-analysis skill

---

# Tools

- JFR: always-on production flight recorder, ~1% overhead. `-XX:StartFlightRecording=...` or `jcmd JFR.start`. Analyze in JDK Mission Control.
- async-profiler: CPU/alloc/lock flame graphs, low overhead, attaches live.
- GC logs: enable everywhere: `-Xlog:gc*:file=gc.log:time,uptime:filecount=5,filesize=20m`. Analyze with GCViewer/gceasy.
- `jcmd`: thread dumps, heap info, native memory (`VM.native_memory` with NMT enabled).

Flame graph reading: width = time. Look for unexpectedly wide frames — serialization, regex, logging, reflection are classic surprises.

---

# GC Selection

| Collector | Choose when |
|---|---|
| G1 (default) | Balanced default for most services |
| ZGC (generational) | Latency-sensitive; sub-ms pauses on large heaps |
| Parallel | Pure throughput batch jobs; pauses irrelevant |
| Serial | Tiny heaps, single CPU containers |

Tuning order for G1:

1. Right-size the heap (`-Xms` = `-Xmx` in containers to avoid resize churn)
2. `-XX:MaxGCPauseMillis` target (realistic — 200ms default, don't demand 10)
3. Only then specific flags, each justified by GC log evidence

GC symptoms:

- Frequent young GCs → high allocation rate: profile allocations, cut garbage (builders reused, avoid boxing in hot loops, right-size collections)
- Long mixed/full GCs → heap too small, humongous objects (G1: objects > half region), or promotion pressure
- Growing old gen that never recovers → leak, not tuning

---

# Container Awareness

- Modern JVMs read cgroup limits; heap default is 25% of container memory — usually too small. Set `-XX:MaxRAMPercentage=50-75` depending on non-heap needs.
- TOTAL JVM footprint = heap + metaspace + code cache + thread stacks + native/direct buffers. Container limit must fit ALL of it, or the OOM killer strikes with exit 137 (no Java OOM error).
- Few-CPU containers: check `Runtime.availableProcessors()` drives sensible pool sizes; consider GC thread counts on tiny pods.

---

# JIT and Warmup

- Expect slow first requests: interpretation → C1 → C2 takes thousands of invocations. Readiness probes should allow warmup or use warmup requests.
- Recurring slowness after deploys = warmup, not regression. Consider CDS/AppCDS, or CRaC/AOT (Leyden) for fast-start needs.
- Microbenchmarks: JMH only. Hand-rolled timing loops measure the JIT, not your code.

---

# Common Findings

- `-Xmx` copy-pasted from another service without sizing
- Container OOMKills misdiagnosed as heap leaks (native footprint ignored)
- Pause-time symptoms "fixed" by bigger heap (often makes pauses longer)
- Thread pools sized `nCPUs` for I/O-bound work (starvation) or 500 for CPU-bound (context-switch thrash)
- Logging or JSON serialization dominating CPU flame graphs unnoticed
- Ancient flags carried forward (`-XX:+UseConcMarkSweepGC` on a JVM that removed CMS)

---

# Output Format

## Symptom

Quantified problem statement.

## Evidence

Profiles, GC log excerpts, flame graph findings.

## Diagnosis

Bottleneck class and mechanism.

## Changes

Each change with expected effect and the measurement that will verify it.

## Results

Before/after numbers.
