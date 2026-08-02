---
name: system-design
description: Design scalable, maintainable, and resilient software systems by evaluating requirements, constraints, and architectural trade-offs before selecting technologies or patterns.
---

---

name: system-design

description: Design scalable, maintainable, and resilient software systems by evaluating requirements, constraints, and architectural trade-offs before selecting technologies or patterns.

---

# System Design

## Goal

Design systems that satisfy business requirements while balancing simplicity, scalability, maintainability, reliability, security, and operational complexity.

Technology choices are a consequence of good design—not the starting point.

---

# When to Use

Use this skill whenever:

- Designing a new service

- Breaking a monolith into microservices

- Creating a new architecture

- Reviewing an architecture proposal

- Scaling an existing system

- Evaluating distributed systems

- Choosing between architectural patterns

- Preparing for high traffic or high availability

---

# Core Philosophy

Follow these principles:

- Requirements before technologies.

- Simplicity before complexity.

- Evolution before perfection.

- Trade-offs over absolutes.

- Operational excellence is part of design.

Every architectural decision introduces costs. Make those costs explicit.

---

# Design Workflow

## Step 1 — Understand the Problem

Clarify:

- What business problem is being solved?

- Who are the users?

- What are the critical user journeys?

- What does success look like?

Do not start drawing components before understanding the problem.

---

## Step 2 — Gather Requirements

### Functional Requirements

Identify:

- Features

- Business rules

- User interactions

- External integrations

- Data ownership

### Non-Functional Requirements

Determine:

- Expected traffic

- Availability targets

- Latency targets

- Throughput

- Scalability

- Durability

- Security

- Compliance

- Cost constraints

Many architectural decisions are driven by non-functional requirements.

---

## Step 3 — Define Constraints

Consider:

- Existing systems

- Team expertise

- Budget

- Deployment model

- Cloud provider

- Regulatory requirements

- Existing contracts

- Time-to-market

The best architecture within the constraints is better than the perfect architecture that cannot be delivered.

---

## Step 4 — Model the Domain

Identify:

- Core entities

- Aggregates

- Relationships

- Ownership

- Boundaries

Align services with business capabilities rather than database tables.

---

## Step 5 — Define System Boundaries

For each service, answer:

- What does it own?

- What does it expose?

- What does it consume?

- What data is authoritative?

- What responsibilities are intentionally excluded?

Avoid overlapping ownership.

---

## Step 6 — Data Flow

Describe:

Request

↓

Validation

↓

Business Logic

↓

Persistence

↓

Events

↓

Consumers

↓

Notifications

Ensure every step has a clear responsibility.

---

# Architecture Principles

## High Cohesion

Keep related responsibilities together.

## Low Coupling

Minimize dependencies between components.

## Explicit Contracts

Communicate through stable APIs or events.

## Single Source of Truth

Each piece of data should have one authoritative owner.

---

# Choosing an Architecture

Do not default to microservices.

Choose the simplest architecture that satisfies current and near-term requirements.

### Monolith

Prefer when:

- Small team

- Shared domain

- Rapid iteration

- Moderate scale

### Modular Monolith

Prefer when:

- One deployment

- Clear domain boundaries

- Preparing for future extraction

### Microservices

Prefer when:

- Independent scaling is required

- Teams own separate domains

- Different deployment cadences exist

- Operational maturity is high

Microservices increase operational complexity. Use them intentionally.

---

# Data Management

Determine:

- Transaction boundaries

- Consistency requirements

- Ownership

- Read/write patterns

- Retention policies

Avoid shared databases between independent services.

---

# Communication

Choose based on requirements.

### Synchronous (REST/gRPC)

Use when:

- Immediate response required

- Strong consistency needed

- User-facing operations

### Asynchronous (Events/Messaging)

Use when:

- Loose coupling is desired

- Eventual consistency is acceptable

- Long-running workflows exist

- Reliability through retries is important

Document the consistency model explicitly.

---

# Scalability

Evaluate:

- Horizontal scaling

- Stateless services

- Caching

- Database partitioning

- Load balancing

- Queue-based workloads

Scale only the bottleneck.

---

# Reliability

Design for:

- Retries

- Timeouts

- Circuit breakers

- Idempotency

- Graceful degradation

- Backpressure

Assume dependencies will fail.

---

# Security

Review:

- Authentication

- Authorization

- Encryption

- Secret management

- Audit logging

- Least privilege

- Data protection

Security must be part of the architecture, not added later.

---

# Observability

Every service should expose:

- Structured logs

- Metrics

- Distributed traces

- Health checks

- Readiness probes

- Liveness probes

If you cannot observe it, you cannot operate it.

---

# Deployment Considerations

Plan for:

- Zero-downtime deployments

- Rollback strategy

- Feature flags

- Backward compatibility

- Schema evolution

Deployment is part of system design.

---

# Trade-off Analysis

For every major decision, document:

### Decision

What was chosen?

### Alternatives

What else was considered?

### Pros

Benefits.

### Cons

Costs.

### Why This Choice

Why it best satisfies the requirements.

Avoid "best practice" arguments without context.

---

# Common Anti-Patterns

Avoid:

- Distributed monoliths

- Shared databases across services

- Premature microservices

- Chatty service-to-service communication

- God services

- Circular dependencies

- Over-engineering

- Underestimating operational complexity

---

# Deliverable

## Problem Summary

## Functional Requirements

## Non-Functional Requirements

## Constraints

## Proposed Architecture

## Data Flow

## Technology Choices

Explain why each technology is appropriate.

## Risks

## Trade-offs

## Future Evolution

Describe how the design can grow without major rewrites.

---

# Guiding Principle

The best architecture is not the most sophisticated one.

It is the simplest design that satisfies today's requirements while allowing tomorrow's changes without unnecessary complexity.