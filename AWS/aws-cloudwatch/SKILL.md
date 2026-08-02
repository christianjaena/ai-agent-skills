---
name: aws-cloudwatch
description: Instrument, monitor, and troubleshoot AWS workloads with CloudWatch covering logs, metrics, alarms, dashboards, Logs Insights queries, and cost control. Use this skill when setting up monitoring and alerting, writing Logs Insights queries, designing dashboards, or investigating issues through CloudWatch data.
---

# aws-cloudwatch

Instrument, monitor, and troubleshoot AWS workloads with CloudWatch covering logs, metrics, alarms, dashboards, Logs Insights queries, and cost control.

## Goal

Make AWS workloads observable: the right metrics with alarms that fire before users notice, logs that answer questions quickly, and a bill that doesn't explode.

---

# When to Use

Use this skill whenever:

- Adding monitoring or alerting to a service
- Writing CloudWatch Logs Insights queries during investigations
- Designing dashboards
- Reviewing alarm coverage for production readiness
- Reducing CloudWatch costs

---

# Logging

- Emit structured JSON logs — Logs Insights parses JSON fields automatically.
- Include on every line: level, timestamp, request/correlation ID, and service name.
- Set retention on EVERY log group. The default is Never Expire and it silently accumulates cost. 30–90 days typical; archive to S3 if longer needed.
- Never log secrets, tokens, or unmasked PII; use data protection policies for defense in depth.
- Lambda: use the built-in JSON log format; API Gateway/ALB: enable access logs for request-level debugging.

## Logs Insights Essentials

```
fields @timestamp, @message
| filter level = "ERROR"
| sort @timestamp desc
| limit 100
```

Useful patterns:

- Error frequency: `stats count(*) by bin(5m)` filtered to errors
- Group by error type: `stats count(*) by errorType | sort count desc`
- Latency percentiles from JSON field: `stats pct(durationMs, 50), pct(durationMs, 99) by bin(5m)`
- Find one request across services: filter by correlation ID across multiple log groups

---

# Metrics

- Prefer Embedded Metric Format (EMF) or metric filters over `PutMetricData` calls (no API latency/cost in the hot path).
- Dimensions multiply cost: every unique dimension combination is a separate custom metric. Never use unbounded values (user ID, request ID) as dimensions.
- Standard resolution (60s) is fine for most things; high resolution (1s) only where alarms need it.
- Track the four golden signals per service: latency (p99, not just average), traffic, errors, saturation.

---

# Alarms

- Alarm on symptoms users feel (error rate, p99 latency, queue age), then on causes (CPU, memory, connections) for diagnosis.
- Use percentiles for latency alarms; averages hide pain.
- `TreatMissingData` matters: for error-rate alarms, missing data usually means "no traffic", not "healthy" — choose `notBreaching` or `breaching` deliberately.
- Composite alarms to reduce noise: page when error rate AND traffic are abnormal, not for each sub-signal.
- Every alarm needs an owner and an action. Alarms that page nobody or always get ignored should be fixed or deleted.

Baseline alarm set per service:

- Error rate above threshold
- p99 latency above SLO
- DLQ depth > 0
- Lambda throttles > 0
- SQS `ApproximateAgeOfOldestMessage` above threshold
- Anomaly detection for traffic drops

---

# Dashboards

- One overview dashboard per service: golden signals top-left, dependencies below.
- Dashboards answer "is it healthy?" in 5 seconds; details live in drill-down dashboards.
- Use alarms widgets so state is visible at a glance.
- Define dashboards in IaC, not by hand.

---

# Cost Control

Biggest CloudWatch cost drivers, in typical order:

1. Log ingestion — reduce log volume (drop debug in prod), use the Infrequent Access log class for rarely queried logs
2. Custom metrics — audit dimension cardinality
3. Log retention — set retention everywhere
4. Dashboards and alarms — usually minor, but delete dead ones
5. Logs Insights scans — narrow time ranges and log groups in queries

---

# Common Review Findings

- Log groups without retention policies
- Unstructured printf logging
- Latency alarms on averages
- No DLQ or queue-age alarms on async pipelines
- High-cardinality metric dimensions
- Alarms with no actions configured
- Debug logging enabled in production at scale
