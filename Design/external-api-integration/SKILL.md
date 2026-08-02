---
name: external-api-integration
description: Design, implement, and review integrations with external APIs by ensuring reliability, resilience, security, observability, and maintainability. Use this skill whenever communicating with third-party or internal services
---

# ---

name: external-api-integration

description: Design, implement, and review integrations with external APIs by ensuring reliability, resilience, security, observability, and maintainability. Use this skill whenever communicating with third-party or internal services.

---

# External API Integration

## Goal

Build reliable integrations with external services that remain secure, resilient, observable, and maintainable even when dependencies fail.

Assume every external dependency will eventually become slow, unavailable, or return unexpected responses.

Design integrations to fail gracefully.

---

# When to Use

Use this skill whenever:

- Calling REST APIs

- Calling GraphQL APIs

- Consuming SOAP services

- Integrating payment providers

- Connecting to identity providers

- Calling internal microservices

- Building webhook consumers

- Building webhook publishers

- Reviewing API integrations

---

# Core Principles

- Never trust external systems.

- Timeouts are mandatory.

- Retries should be intentional.

- Fail gracefully.

- Make integrations observable.

- Handle partial failures.

- Preserve idempotency.

---

# Integration Workflow

## Step 1 — Understand the API

Determine:

- Purpose

- Authentication

- Rate limits

- SLA

- Timeout expectations

- Retry policy

- Error responses

- Versioning

Read the API documentation before implementation.

---

## Step 2 — Define the Contract

Document:

- Request model

- Response model

- Required headers

- Authentication

- Error responses

- Pagination

- Idempotency requirements

Avoid leaking external DTOs into your domain model.

---

## Step 3 — Authentication

Review:

- OAuth2

- API Keys

- JWT

- Mutual TLS

- Signed requests

Never hardcode credentials.

Use secure secret management.

---

## Step 4 — Timeouts

Every outbound request should define:

- Connection timeout

- Read timeout

- Write timeout (if applicable)

Never rely on default timeout values.

---

## Step 5 — Retry Strategy

Retry only when appropriate.

Good retry candidates:

- Temporary network failures

- HTTP 502

- HTTP 503

- HTTP 504

- Connection resets

Avoid retrying:

- Validation failures

- Authentication failures

- Authorization failures

- Business rule violations

Use exponential backoff with jitter.

Avoid aggressive retry storms.

---

## Step 6 — Circuit Breakers

Use circuit breakers for:

- Frequently failing services

- Slow dependencies

- Critical integrations

Review:

- Failure threshold

- Recovery timeout

- Half-open behavior

Prevent cascading failures.

---

## Step 7 — Idempotency

Review operations that may be retried.

Examples:

- Payments

- Order creation

- Loan disbursement

- Webhooks

Support idempotency keys when duplicate requests are possible.

---

## Step 8 — Error Handling

Handle:

- Timeouts

- Network failures

- Invalid responses

- Unexpected status codes

- Malformed payloads

- Rate limiting

Never expose raw external errors directly to clients.

Translate them into domain-specific errors.

---

## Step 9 — Resilience

Design for failure.

Consider:

- Retries

- Circuit breakers

- Fallbacks

- Cached responses

- Graceful degradation

Not every integration requires every pattern.

Choose based on business impact.

---

## Step 10 — Observability

Log:

- Request initiation

- Response status

- Duration

- Retries

- Failures

- Correlation ID

Collect metrics:

- Latency

- Success rate

- Failure rate

- Retry count

- Timeout count

Trace every outbound request.

---

# DTO Design

Separate:

External DTO

↓

Mapper

↓

Domain Model

Avoid exposing third-party models throughout the application.

---

# Spring Boot Guidelines

Prefer:

- WebClient

- RestClient (Spring 6.1+)

Use:

- Connection pooling

- Timeouts

- Interceptors

- Centralized configuration

Avoid creating HTTP clients inside business services.

---

# Webhook Consumers

Verify:

- Signature validation

- Authentication

- Idempotency

- Retry handling

- Event ordering

- Duplicate delivery

Never assume a webhook will be delivered exactly once.

---

# Webhook Publishers

Ensure:

- Retry strategy

- Dead letter handling

- Delivery logging

- Backoff strategy

- Timeout configuration

---

# Common Mistakes

Avoid:

- Infinite retries

- Missing timeouts

- Hardcoded credentials

- Tight coupling to external DTOs

- Ignoring rate limits

- Logging secrets

- Blocking indefinitely

- Missing monitoring

---

# Review Checklist

Verify:

- Authentication

- Timeouts

- Retry policy

- Circuit breaker

- Error handling

- DTO mapping

- Idempotency

- Observability

- Secret management

- Documentation

---

# Deliverable

## Integration Summary

Describe the purpose of the integration.

## Authentication

Describe how authentication is handled.

## Request & Response Models

Summarize contracts and mappings.

## Resilience Strategy

Explain retries, timeouts, circuit breakers, and fallbacks.

## Error Handling

Describe expected failure handling.

## Observability

Review logging, metrics, tracing, and alerts.

## Risks

Identify operational or business risks.

## Recommendations

Provide prioritized improvements.

---

# Guiding Principle

External systems are outside your control.

Build integrations that remain predictable, observable, and resilient even when those systems are slow, unavailable, or behave unexpectedly.