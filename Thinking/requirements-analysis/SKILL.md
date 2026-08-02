---
name: requirements-analysis
description: Analyze requirements before implementation by identifying goals, constraints, assumptions, edge cases, dependencies, and ambiguities. Use this skill before making any significant code changes.
---

# ---

name: requirements-analysis

description: Analyze requirements before implementation by identifying goals, constraints, assumptions, edge cases, dependencies, and ambiguities. Use this skill before making any significant code changes.

---

# Requirements Analysis

## Goal

Understand the problem completely before writing or modifying code.

This skill exists to prevent incorrect implementations caused by misunderstood requirements.

Do not begin implementation until the problem is well understood.

---

# When to Use

Use this skill whenever:

- Implementing a new feature

- Fixing a bug

- Refactoring significant code

- Designing an API

- Planning a migration

- Investigating production issues

- Reviewing unclear tickets

- Estimating implementation effort

---

# Core Principles

- Understand before implementing.

- Clarify before assuming.

- Prefer facts over guesses.

- Every assumption should be explicit.

- Ambiguity is a risk that should be reduced.

---

# Analysis Workflow

## Step 1 — Identify the Objective

Determine:

- What problem is being solved?

- Who is affected?

- What is the expected outcome?

- Why is this change needed?

Summarize the objective in one or two sentences before continuing.

---

## Step 2 — Gather Requirements

Identify:

### Functional Requirements

- Expected behavior

- Inputs

- Outputs

- Business rules

- Validation

- Error handling

### Non-Functional Requirements

- Performance expectations

- Security requirements

- Scalability

- Reliability

- Availability

- Maintainability

- Observability

---

## Step 3 — Identify Constraints

Consider:

- Existing architecture

- Backward compatibility

- API contracts

- Database schema

- Third-party integrations

- Deployment strategy

- Team conventions

- Regulatory or compliance requirements

Constraints often determine the correct implementation.

---

## Step 4 — Identify Dependencies

List:

- Services involved

- Repositories

- External APIs

- Event publishers or consumers

- Scheduled jobs

- Configuration

- Feature flags

- Infrastructure components

Understand how the requested change interacts with the rest of the system.

---

## Step 5 — Identify Edge Cases

Consider:

- Null or missing data

- Empty collections

- Duplicate requests

- Invalid input

- Unauthorized access

- Large datasets

- Time zones

- Concurrent requests

- Partial failures

- Retry scenarios

Do not optimize only for the happy path.

---

## Step 6 — Identify Risks

Examples:

- Breaking existing APIs

- Database migrations

- Performance regressions

- Security issues

- Concurrency problems

- Increased operational complexity

Document significant risks before implementation.

---

## Step 7 — Identify Unknowns

Explicitly list:

- Missing requirements

- Conflicting requirements

- Assumptions

- Information that requires clarification

Never silently invent business behavior.

---

# Questions to Ask

When requirements are incomplete, ask concise, targeted questions.

Examples:

- What is the expected behavior when the resource does not exist?

- Should this operation be idempotent?

- Are there authorization requirements?

- Should existing clients remain compatible?

- What should happen on validation failure?

- Is this change behind a feature flag?

- Are there performance targets?

- Is there an existing implementation to follow?

Ask only the questions necessary to reduce uncertainty.

---

# Deliverable

Produce a concise analysis before implementation.

## Problem Summary

Briefly restate the requested change.

## Functional Requirements

List the required behaviors.

## Non-Functional Requirements

List quality attributes and constraints.

## Dependencies

Identify affected components and integrations.

## Edge Cases

Highlight scenarios that require special handling.

## Risks

Describe potential implementation or deployment risks.

## Assumptions

State any assumptions explicitly.

## Open Questions

List unresolved items that should be clarified.

---

# Guiding Principle

Measure twice, cut once.

A few minutes spent understanding the problem can prevent hours of debugging and rework.