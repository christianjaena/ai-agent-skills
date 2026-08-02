---
name: production-support
description: Respond to production incidents by assessing impact, restoring service safely, investigating root causes, coordinating mitigations, and planning permanent fixes. Use this skill during live incidents and operational support.
---

# ---

name: production-support

description: Respond to production incidents by assessing impact, restoring service safely, investigating root causes, coordinating mitigations, and planning permanent fixes. Use this skill during live incidents and operational support.

---

# Production Support

## Goal

Restore production service safely while minimizing customer impact.

During an incident:

1. Stabilize the system.

2. Gather evidence.

3. Restore service.

4. Identify root cause.

5. Prevent recurrence.

Do not optimize or refactor during an active incident unless necessary to restore service.

---

# When to Use

Use this skill whenever:

- Production incidents occur

- APIs return unexpected errors

- Lambda executions timeout

- Kubernetes pods restart repeatedly

- Services become unavailable

- Database connectivity fails

- Queues build up

- Memory or CPU usage spikes

- Alerts fire

- Customers report production issues

---

# Core Principles

People first.

Systems second.

Code third.

During an incident:

- Reduce customer impact.

- Preserve evidence.

- Avoid unnecessary changes.

- Make reversible decisions.

- Communicate clearly.

---

# Incident Workflow

## Step 1 — Assess Severity

Determine:

- What is broken?

- Who is affected?

- How many users are impacted?

- Is data integrity at risk?

- Is the issue ongoing?

Classify severity.

Example:

- Critical

- High

- Medium

- Low

---

## Step 2 — Stabilize the System

Consider:

- Rollback deployment

- Enable feature flags

- Scale infrastructure

- Restart unhealthy instances

- Pause consumers

- Disable scheduled jobs

- Rate limit requests

- Fail over to a secondary service

Prefer the least risky mitigation that restores service quickly.

---

## Step 3 — Gather Evidence

Collect before making major changes:

- Logs

- Metrics

- Traces

- Stack traces

- Thread dumps

- Heap dumps (if appropriate)

- Deployment history

- Recent configuration changes

- Database metrics

- Infrastructure events

Do not rely on memory or assumptions.

---

## Step 4 — Determine the Scope

Identify:

- Affected services

- APIs

- Databases

- Message queues

- Scheduled jobs

- External dependencies

- Regions

- Customer segments

Incidents often extend beyond the initially reported symptom.

---

## Step 5 — Identify the Trigger

Look for:

- Recent deployments

- Configuration changes

- Infrastructure failures

- Certificate expiration

- Dependency outages

- Database migrations

- Increased traffic

- External API failures

Always compare current behavior with the last known healthy state.

---

## Step 6 — Apply a Mitigation

Possible mitigations:

- Rollback

- Roll forward with a hotfix

- Disable a feature

- Increase capacity

- Clear a stuck queue

- Restart unhealthy workloads

- Restore connectivity

- Rebuild caches

Mitigations should be reversible whenever possible.

---

## Step 7 — Verify Recovery

Confirm:

- Alerts cleared

- Error rate decreased

- Latency returned to normal

- Customers can complete critical workflows

- Queue depth stabilizes

- Resource usage normalizes

Recovery should be verified with metrics, not assumptions.

---

# Spring Boot Checklist

Review:

- Startup failures

- Bean initialization errors

- Transaction failures

- Connection pool exhaustion

- Thread pool saturation

- Database locks

- Configuration issues

- Exception logs

---

# AWS Checklist

Review:

- Lambda duration

- Lambda cold starts

- CloudWatch alarms

- SQS queue depth

- SNS delivery failures

- IAM permission changes

- RDS metrics

- API Gateway errors

---

# Kubernetes Checklist

Review:

- Pod status

- Restart count

- OOMKilled

- CrashLoopBackOff

- Readiness probes

- Liveness probes

- Resource limits

- CPU throttling

- Memory pressure

- HPA scaling events

---

# Database Checklist

Check:

- Slow queries

- Lock contention

- Connection pool usage

- Replication lag

- Failed migrations

- Deadlocks

- Disk utilization

---

# Communication

Provide regular updates including:

- Current impact

- Mitigation status

- Estimated recovery time (if known)

- Risks

- Next actions

Communicate facts.

Avoid speculation.

---

# Things to Avoid

During an incident, avoid:

- Large refactorings

- Unrelated improvements

- Guessing root causes

- Multiple simultaneous fixes

- Deleting evidence

- Ignoring monitoring

Stability takes priority over elegance.

---

# After Recovery

Once the system is stable:

- Collect additional evidence.

- Document the timeline.

- Identify the root cause.

- Create follow-up tasks.

- Improve monitoring.

- Update runbooks.

- Add regression tests.

- Review deployment process.

Every incident should improve the system.

---

# Deliverable

## Incident Summary

Brief description of the issue.

## Impact

Who or what was affected?

## Timeline

Chronological sequence of key events.

## Evidence

Logs, metrics, traces, and observations.

## Root Cause

Known or suspected cause.

## Mitigation

Actions taken to restore service.

## Permanent Fix

Changes required to prevent recurrence.

## Follow-up Actions

- Monitoring improvements

- Tests

- Documentation

- Runbook updates

- Technical debt

---

# Guiding Principle

The objective of production support is not to prove why something failed.

It is to restore service safely, learn from the incident, and leave the system more resilient than before.