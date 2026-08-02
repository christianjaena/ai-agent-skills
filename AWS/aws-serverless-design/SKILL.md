---
name: aws-serverless-design
description: Design serverless architectures on AWS composing Lambda, API Gateway, SQS, SNS, EventBridge, DynamoDB, Step Functions, and S3 with correct patterns for resilience, cost, and scale. Use this skill when designing new serverless systems, reviewing serverless architectures, or choosing between serverless services and patterns.
---

# aws-serverless-design

Design serverless architectures on AWS composing Lambda, API Gateway, SQS, SNS, EventBridge, DynamoDB, Step Functions, and S3 with correct patterns for resilience, cost, and scale.

## Goal

Compose managed services into architectures that scale to zero and to spike, fail partially instead of totally, and remain debuggable and affordable.

---

# When to Use

Use this skill whenever:

- Designing a new serverless system or feature
- Reviewing a proposed serverless architecture
- Choosing between Lambda, Fargate, Step Functions, or containers
- Deciding how services should communicate (sync vs async)
- Evaluating cost and scaling characteristics of a design

---

# Design Principles

- Async by default: synchronous chains couple availability and latency of every hop. Buffer with queues wherever the caller doesn't need an immediate answer.
- Every component fails: design for retries, duplicates, and partial failure from day one — idempotency everywhere, DLQs everywhere.
- Let managed services do the work: prefer service-to-service integrations (S3 → EventBridge, API Gateway → SQS direct integrations) over Lambda glue code that just forwards data.
- Statelessness: state lives in DynamoDB/S3/Step Functions, never in function memory.
- Design for observability: correlation IDs propagate through every hop; X-Ray or equivalent tracing across the chain.

---

# Choosing Compute

| Workload | Choice |
|---|---|
| Event-driven, spiky, < 15 min tasks | Lambda |
| Long-running, steady traffic, large images | Fargate/ECS |
| Multi-step workflows with retries, waits, human approval | Step Functions orchestrating Lambdas |
| Ultra-low-latency APIs with steady high traffic | Consider containers; Lambda cost and cold starts may lose |

Watch for the cost crossover: at sustained high utilization, always-on containers become cheaper than Lambda.

---

# Communication Patterns

- API Gateway → Lambda: synchronous request/response. Keep the chain ONE hop; Lambda calling Lambda synchronously is an anti-pattern (use queues or Step Functions).
- SNS → SQS fan-out: one event, many independent consumers with their own retry/DLQ.
- EventBridge: event bus between domains; content-based routing; the default for domain events between services.
- SQS: load leveling in front of anything with limited throughput (databases, third-party APIs).
- Step Functions: orchestration with built-in retries, catches, and state — instead of hand-rolled saga code in Lambdas.

Choreography (events) between bounded contexts; orchestration (Step Functions) within a workflow.

---

# Data

- DynamoDB is the default serverless database: single-digit ms, scales with traffic, on-demand billing. Model access patterns first (single-table design where appropriate).
- DynamoDB Streams for change-driven processing (with Lambda + idempotency).
- S3 for blobs + claim check pattern for large message payloads.
- RDS with Lambda requires RDS Proxy for connection pooling — raw connections exhaust the database under Lambda scale.
- Aurora Serverless v2 when relational is required with variable load.

---

# Resilience Checklist

- Idempotent consumers on all at-least-once sources
- DLQ + alarm on every async path
- Timeouts and retries with backoff + jitter on every outbound call
- Reserved concurrency protecting non-scaling downstreams
- Circuit breaker or queue in front of fragile third parties
- Throttling/quotas at API Gateway (usage plans, WAF)
- Multi-AZ is automatic; multi-region only if the business case justifies its complexity

---

# Cost Review

- Biggest surprise costs: API Gateway per-request pricing at scale (consider HTTP APIs or ALB), CloudWatch log ingestion, Lambda over-provisioned memory, DynamoDB on-demand at sustained high load (switch to provisioned + autoscaling), NAT gateway data processing for VPC Lambdas.
- Estimate cost at expected AND 10x traffic before committing to a design.

---

# Design Review Output Format

## Architecture Summary

Components and data flow (include a diagram).

## Strengths

What the design gets right.

## Risks

For each: scenario, blast radius, likelihood, mitigation.

## Cost Notes

Dominant cost drivers at expected and 10x scale.

## Recommendation

Proceed, proceed with changes, or rework.
