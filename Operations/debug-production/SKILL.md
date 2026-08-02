---
name: debug-production
description: Systematically debug production issues using logs, metrics, traces, and runtime evidence to find the root cause before proposing fixes. Use this skill when investigating production bugs, errors, outages, unexpected behavior, or intermittent failures in live systems.
---

# debug-production

Systematically debug production issues using logs, metrics, traces, and runtime evidence to find the root cause before proposing fixes.

## Goal

Find the true root cause of a production issue using evidence, not guesses. Never propose a fix until the failure mechanism is understood and supported by data.

---

# When to Use

Use this skill whenever:

- Investigating a production bug or error spike
- Diagnosing intermittent or hard-to-reproduce failures
- Analyzing unexpected behavior reported by users
- Investigating data inconsistencies
- Following up on an incident to find root cause
- Debugging issues that only occur in production, not locally

---

# Debugging Philosophy

- Evidence over intuition. Every hypothesis must be confirmed or rejected by data.
- Reproduce before you fix. If you cannot reproduce, you cannot verify the fix.
- Change one thing at a time.
- The most recent change is the most likely suspect, but not the only one.
- Correlation is not causation. A deploy at the same time as an error spike is a lead, not a conclusion.

---

# Debugging Workflow

## 1. Define the Problem Precisely

Before investigating:

- What is the exact observed behavior?
- What is the expected behavior?
- When did it start? Is it ongoing or resolved?
- Who is affected? All users, a subset, one tenant?
- Is it constant, intermittent, or load-dependent?

Never debug a vaguely defined problem. "It's slow" and "it's broken" must be turned into measurable symptoms first.

---

## 2. Gather Evidence

Collect before hypothesizing:

- Error logs and stack traces around the failure window
- Metrics: error rate, latency, throughput, saturation
- Distributed traces for failing requests
- Recent deploys, config changes, feature flag changes
- Infrastructure events: scaling, restarts, node failures, certificate expiry
- Upstream and downstream dependency health

Build a timeline of events leading up to the first failure.

---

## 3. Form Hypotheses

For each hypothesis, state:

- The proposed failure mechanism
- What evidence would confirm it
- What evidence would rule it out

Rank hypotheses by likelihood and cost to verify. Test the cheapest, most likely ones first.

Common production-only causes:

- Configuration or environment differences
- Data shapes not present in test environments
- Concurrency and race conditions under real load
- Resource exhaustion (connections, memory, file handles, threads)
- Dependency timeouts and retry storms
- Clock, timezone, and locale differences
- Cache staleness or cache stampedes

---

## 4. Reproduce

Attempt to reproduce:

- In a staging environment with production-like data
- With a minimal test case that isolates the mechanism
- Under load if the issue is load-dependent

If reproduction is impossible, add targeted logging or metrics to capture the failure the next time it occurs, then wait for evidence.

---

## 5. Identify Root Cause

The root cause explains:

- Why the failure happens
- Why it happens under these specific conditions
- Why it started when it did

If any of these is unexplained, keep digging. "Restarting fixed it" is not a root cause.

Distinguish:

- Trigger: what set the failure off
- Root cause: the underlying defect
- Contributing factors: what made impact worse

---

## 6. Fix and Verify

- Fix the root cause, not the symptom.
- Add a regression test that fails without the fix.
- Verify in staging, then production, using the same metrics that showed the failure.
- Consider whether the same defect exists elsewhere in the codebase.

---

## 7. Prevent Recurrence

After the fix:

- Add alerting that would have caught this earlier
- Add missing logging or tracing discovered during investigation
- Document the failure mechanism for the team

---

# Anti-Patterns

- Guess-and-deploy loops
- Restarting services without capturing diagnostics first
- Blaming the network, GC, or "flakiness" without evidence
- Fixing symptoms (retries, timeouts bumped) while leaving the defect
- Deleting evidence (logs, heap dumps, crashed pods) before analysis

---

# Output Format

Structure findings as:

## Symptom

What was observed, when, and impact scope.

## Timeline

Key events in order.

## Root Cause

The failure mechanism, with supporting evidence.

## Fix

The change made and how it was verified.

## Prevention

Alerts, tests, or monitoring added to catch this class of issue earlier.
