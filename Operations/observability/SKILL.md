---
name: observability
description: Design and review observability for applications by evaluating logging, metrics, tracing, dashboards, alerts, and health endpoints. Use this skill when implementing services, reviewing production readiness, or improving operational visibility
---

---

name: observability

description: Design and review observability for applications by evaluating logging, metrics, tracing, dashboards, alerts, and health endpoints. Use this skill when implementing services, reviewing production readiness, or improving operational visibility.

---

# Observability

## Goal

Design systems that are easy to monitor, troubleshoot, and operate in production.

A production system should answer:

- What happened?

- Why did it happen?

- Who was affected?

- How often does it happen?

- Is it getting worse?

- What should we do next?

Observability should be built into the system—not added after an incident.

---

# When to Use

Use this skill whenever:

- Developing a new service

- Implementing a new API

- Reviewing production readiness

- Investigating incidents

- Improving monitoring

- Creating dashboards

- Defining alerts

- Designing microservices

- Building event-driven systems

---

# Core Principles

- Every request should be traceable.

- Every important event should be measurable.

- Logs should explain.

- Metrics should quantify.

- Traces should connect.

- Alerts should be actionable.

Never rely on logs alone.

---

# Observability Pillars

## Logging

Logs explain **what happened**.

Logs should be:

- Structured (JSON preferred)

- Consistent

- Searchable

- Context-rich

- Free of sensitive information

Include:

- Timestamp

- Log level

- Service name

- Environment

- Request ID

- Correlation ID

- User ID (if appropriate)

- Operation

- Duration

- Error details

Avoid:

- Passwords

- Tokens

- Secrets

- Credit card numbers

- Personal information

---

## Metrics

Metrics explain **how the system behaves over time**.

Measure:

- Request count

- Error count

- Error rate

- Response time

- Throughput

- CPU

- Memory

- Database latency

- Queue depth

- Cache hit ratio

Track trends instead of isolated values.

---

## Tracing

Tracing explains **where time is spent**.

Every distributed request should be traceable.

Trace:

Client

↓

API Gateway

↓

Controller

↓

Service

↓

Database

↓

External APIs

↓

Events

↓

Consumers

Every service should propagate trace context.

---

# Logging Guidelines

Log:

- Startup

- Shutdown

- Important business events

- External API calls

- Retries

- Failures

- Validation failures

- Security events

Avoid excessive debug logging in production.

---

# Metrics Guidelines

Prefer business metrics in addition to technical metrics.

Examples:

Technical:

- P95 latency

- CPU utilization

- JVM heap usage

Business:

- Orders created

- Loans approved

- Payments processed

- Failed transactions

Business metrics often reveal customer impact faster than infrastructure metrics.

---

# Health Endpoints

Provide:

- Liveness

- Readiness

Readiness should verify:

- Database

- Message broker

- Cache

- Required dependencies

Avoid expensive health checks.

---

# Alerting

Alerts should be:

- Actionable

- Specific

- Low-noise

Alert on:

- High error rates

- Increased latency

- Service unavailable

- Queue backlog

- Failed scheduled jobs

- Resource exhaustion

Do not alert on every warning log.

---

# Dashboards

Dashboards should answer:

- Is the service healthy?

- Are customers impacted?

- What changed?

- Where is the bottleneck?

Include:

- Traffic

- Errors

- Latency

- Saturation

- Deployment markers

- Infrastructure metrics

- Business KPIs

---

# Correlation IDs

Every request should include a correlation ID.

Use it consistently across:

- Logs

- Events

- External API calls

- Background jobs

Correlation IDs make distributed debugging dramatically easier.

---

# Spring Boot Checklist

Review:

- Structured logging

- Exception logging

- Micrometer metrics

- Actuator endpoints

- Health indicators

- MDC usage

- Request logging

- Correlation IDs

---

# Kubernetes Checklist

Monitor:

- Pod restarts

- CPU usage

- Memory usage

- Restart count

- HPA events

- Probe failures

- Node pressure

---

# AWS Checklist

Monitor:

- CloudWatch Logs

- Lambda duration

- Lambda errors

- Cold starts

- API Gateway metrics

- SQS queue depth

- SNS delivery failures

- RDS metrics

---

# Common Mistakes

Avoid:

- Logging everything

- Logging nothing

- Missing correlation IDs

- High-cardinality metrics

- Alerts without runbooks

- Dashboards nobody uses

- Missing business metrics

---

# Review Checklist

Verify:

- Structured logs

- Useful metrics

- Distributed tracing

- Correlation IDs

- Health endpoints

- Actionable alerts

- Operational dashboards

- Business metrics

---

# Deliverable

## Observability Assessment

Summarize the current observability posture.

## Logging Review

Strengths and gaps.

## Metrics Review

Existing metrics and missing metrics.

## Tracing Review

Coverage and missing trace propagation.

## Alert Review

Noise, gaps, and recommendations.

## Dashboard Review

Missing operational or business dashboards.

## Recommendations

Prioritized improvements with expected operational impact.

---

# Guiding Principle

If an engineer cannot determine what happened within a few minutes using logs, metrics, traces, and dashboards, the system is not sufficiently observable.

Good observability reduces incident duration, improves reliability, and enables confident deployments.