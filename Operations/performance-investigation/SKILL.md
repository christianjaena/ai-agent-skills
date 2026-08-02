---
name: performance-investigation
description: Investigate and resolve performance issues using evidence-driven analysis. Identify bottlenecks before proposing optimizations. Use this skill whenever an application is slow, resource-intensive, or failing to meet performance expectations
---

---

name: performance-investigation

description: Investigate and resolve performance issues using evidence-driven analysis. Identify bottlenecks before proposing optimizations. Use this skill whenever an application is slow, resource-intensive, or failing to meet performance expectations.

---

# Performance Investigation

## Goal

Identify the true performance bottleneck before making changes.

Never optimize based on assumptions.

Performance work should always be driven by measurements and evidence.

---

# When to Use

Use this skill whenever:

- APIs are slow

- Database queries are slow

- CPU usage is high

- Memory usage is high

- Startup time is slow

- Requests timeout

- Lambda executions are slow

- Kubernetes pods are throttled

- Users report slowness

- Throughput decreases

- Latency increases

---

# Core Principles

Measure first.

Optimize second.

Measure again.

If you cannot measure the improvement, you cannot prove the optimization.

---

# Investigation Workflow

## Step 1 — Define the Problem

Determine:

- What is slow?

- Since when?

- Is the issue reproducible?

- Does it affect all users?

- Does it occur under load?

- Which environment is affected?

Quantify the issue whenever possible.

Examples:

- API latency increased from 180ms to 850ms.

- Memory usage doubled after deployment.

- P95 latency exceeds SLA.

---

## Step 2 — Gather Evidence

Collect:

- Application logs

- Metrics

- Distributed traces

- Profiling data

- JVM statistics

- SQL execution plans

- Thread dumps

- Heap dumps

- Kubernetes metrics

- Cloud monitoring dashboards

Never recommend changes without evidence.

---

## Step 3 — Identify the Bottleneck

Possible bottlenecks include:

### CPU

Symptoms:

- High CPU usage

- Slow computations

- Excessive serialization

- Compression

- Encryption

---

### Memory

Symptoms:

- Frequent GC

- OutOfMemoryError

- High heap usage

- Allocation spikes

---

### Database

Symptoms:

- Slow queries

- Missing indexes

- N+1 queries

- Full table scans

- Lock contention

---

### Network

Symptoms:

- High response times

- External API latency

- DNS delays

- Connection pooling issues

---

### Threading

Symptoms:

- Deadlocks

- Thread starvation

- Blocking I/O

- Excessive synchronization

---

### Disk

Symptoms:

- Slow file operations

- High I/O wait

- Large log writes

---

# Java Performance Checklist

Review:

- Object allocation

- Stream usage

- Boxing/unboxing

- String concatenation

- Collections

- Synchronization

- Thread pools

- Virtual Threads (if used)

- Reflection

- Serialization

Avoid premature micro-optimizations.

---

# Spring Boot Checklist

Review:

- Bean initialization

- Startup time

- Lazy initialization

- Transaction boundaries

- Request filters

- Jackson serialization

- RestTemplate/WebClient usage

- Caching configuration

---

# Database Investigation

Check:

- Execution plans

- Query count

- Missing indexes

- N+1 queries

- Connection pool saturation

- Lock contention

- Pagination strategy

- Batch operations

Optimize the query before optimizing Java code.

---

# API Investigation

Measure:

- P50

- P95

- P99

- Throughput

- Error rate

One average latency number is insufficient.

---

# JVM Investigation

Inspect:

- Heap usage

- GC frequency

- GC pause time

- Allocation rate

- Thread count

- CPU utilization

- JIT compilation

Use profiling tools before changing code.

---

# Cloud Investigation

For AWS:

Review:

- Lambda duration

- Cold starts

- Memory allocation

- CPU allocation

- Network latency

- Retry behavior

For Kubernetes:

Review:

- CPU throttling

- Memory limits

- Pod restarts

- HPA scaling

- Readiness probes

- Liveness probes

---

# Optimization Strategy

Optimize in this order:

1. Remove unnecessary work.

2. Reduce database calls.

3. Reduce network calls.

4. Improve algorithms.

5. Batch operations.

6. Cache expensive computations.

7. Parallelize only when beneficial.

8. Tune JVM or infrastructure.

Do not skip directly to caching.

---

# Common Mistakes

Avoid:

- Optimizing without benchmarks

- Blaming the database immediately

- Adding caching prematurely

- Increasing hardware instead of fixing code

- Ignoring GC

- Ignoring query plans

- Guessing

---

# Questions to Ask

- What is the current latency?

- What changed recently?

- Is there profiling data?

- Which endpoint is affected?

- Which metric indicates the slowdown?

- Does it happen under load?

- Is the database healthy?

- Are external services involved?

---

# Deliverable

## Problem Summary

Describe the performance issue.

## Evidence

List observed metrics and measurements.

## Bottleneck Analysis

Identify the most likely bottleneck with supporting evidence.

## Optimization Recommendations

Rank recommendations by expected impact.

## Risks

Explain trade-offs or potential side effects.

## Validation Plan

Describe how improvements will be measured after implementation.

---

# Guiding Principle

Performance engineering is a science, not guesswork.

Always optimize the bottleneck, not the code that looks slow.