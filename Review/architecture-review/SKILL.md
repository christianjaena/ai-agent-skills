---
name: architecture-review
description: Review an existing software architecture for maintainability, scalability, modularity, reliability, and adherence to engineering principles. Use this skill when evaluating architectural quality, proposing improvements, or reviewing significant design changes
---

# ---

name: architecture-review

description: Review an existing software architecture for maintainability, scalability, modularity, reliability, and adherence to engineering principles. Use this skill when evaluating architectural quality, proposing improvements, or reviewing significant design changes.

---

# Architecture Review

## Goal

Evaluate an existing architecture to determine whether it is well-structured, maintainable, scalable, resilient, and aligned with the project's requirements.

This skill focuses on reviewing an existing system—not designing a new one.

Provide objective feedback supported by architectural principles and practical trade-offs.

---

# When to Use

Use this skill whenever:

- Reviewing a pull request with architectural impact

- Evaluating an existing application

- Reviewing a monolith or microservice architecture

- Assessing technical debt

- Planning a modernization effort

- Reviewing a framework migration

- Identifying design smells

- Evaluating scalability or maintainability concerns

---

# Core Principles

Evaluate architecture using these principles:

- Simplicity over unnecessary complexity

- High cohesion

- Low coupling

- Clear ownership

- Explicit boundaries

- Separation of concerns

- Evolutionary architecture

- Operational excellence

Avoid recommending large architectural changes unless the benefits clearly outweigh the costs.

---

# Review Workflow

## Step 1 — Understand the System

Identify:

- Business purpose

- Primary users

- Major workflows

- Existing architecture

- Deployment model

- Technology stack

Understand why the architecture exists before criticizing it.

---

## Step 2 — Identify Architectural Style

Determine whether the system is:

- Layered Architecture

- Modular Monolith

- Hexagonal Architecture

- Clean Architecture

- Onion Architecture

- Microservices

- Event-Driven Architecture

- Serverless

- Hybrid

Review the architecture according to its intended style rather than forcing another architectural pattern.

---

## Step 3 — Review Module Boundaries

Evaluate whether responsibilities are clearly separated.

Questions:

- Does every module have a single responsibility?

- Are responsibilities duplicated?

- Are modules tightly coupled?

- Is there clear ownership?

Healthy modules should minimize knowledge of one another.

---

## Step 4 — Review Layering

Verify that responsibilities remain in the correct layer.

For a typical Spring Boot application:

Controller

- HTTP concerns

- Validation

- Request mapping

Service

- Business rules

- Transactions

- Orchestration

Repository

- Persistence only

Domain

- Business model

- Domain behavior

Avoid:

- Business logic in controllers

- SQL in services

- HTTP logic inside domain objects

---

## Step 5 — Evaluate Coupling

Look for:

- Circular dependencies

- Shared mutable state

- Excessive module dependencies

- Direct database access across modules

- Tight framework coupling

Prefer dependency inversion where appropriate.

---

## Step 6 — Evaluate Cohesion

Modules should group related behavior.

Warning signs:

- God classes

- Utility classes containing unrelated logic

- Services with many responsibilities

- Large packages with unrelated concepts

---

## Step 7 — Evaluate Maintainability

Review:

- Naming

- Package structure

- Code organization

- Reusability

- Testability

- Complexity

- Documentation

Ask:

Would a new engineer understand this architecture within a reasonable amount of time?

---

## Step 8 — Evaluate Scalability

Review:

- Statelessness

- Caching

- Database bottlenecks

- Service boundaries

- Event usage

- Horizontal scaling

Recommend scaling improvements only when justified by requirements.

---

## Step 9 — Evaluate Reliability

Review:

- Retry strategy

- Timeout configuration

- Circuit breakers

- Idempotency

- Graceful degradation

- Failure isolation

Assume every dependency will eventually fail.

---

## Step 10 — Evaluate Security

Verify:

- Authentication

- Authorization

- Secret management

- Encryption

- Audit logging

- Sensitive data handling

Architecture should enable secure implementations by default.

---

## Step 11 — Evaluate Observability

Determine whether the system provides:

- Structured logs

- Metrics

- Tracing

- Health endpoints

- Correlation IDs

- Alerting

A production system should be observable without modifying code during an incident.

---

# Spring Boot Architecture Checklist

Review:

- Thin controllers

- Business logic in services

- Repositories limited to persistence

- DTO separation

- Transaction boundaries

- Exception handling

- Configuration organization

- Dependency injection

- Package structure

---

# Common Architecture Smells

Watch for:

- God services

- Fat controllers

- Shared databases across services

- Circular dependencies

- Duplicate business rules

- Anemic domain models

- Excessive abstraction

- Premature microservices

- Feature envy

- Large utility classes

- Hidden coupling

- Copy-and-paste architecture

---

# Review Guidelines

Do not recommend:

- Microservices simply because they are popular

- Design patterns without a demonstrated need

- New abstractions that increase complexity

- Rewrites when incremental improvements are sufficient

Architecture should solve business problems, not showcase patterns.

---

# Severity Levels

Classify findings as:

### Critical

Likely to cause production failures, security issues, or major maintenance problems.

### High

Significant architectural concern that should be addressed soon.

### Medium

Improvement opportunity with moderate impact.

### Low

Minor improvement or consistency issue.

### Positive

Highlight good architectural decisions as well.

---

# Deliverable

## Architecture Summary

Describe the current architecture.

## Strengths

Identify well-designed aspects of the system.

## Findings

For each finding include:

- Severity

- Description

- Impact

- Recommendation

## Technical Debt

Highlight architectural debt that should be prioritized.

## Risks

Describe scalability, maintainability, operational, or security risks.

## Overall Assessment

Provide one of:

- Excellent

- Good

- Needs Improvement

- Significant Concerns

Support the assessment with evidence.

---

# Guiding Principle

A good architecture is not the one with the most patterns.

It is the one that is easiest to understand, simplest to evolve, resilient in production, and well-aligned with the business it serves.