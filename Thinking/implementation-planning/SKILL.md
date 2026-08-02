---
name: implementation-planning
description: Plan software changes before modifying code by identifying affected components, implementation strategy, risks, dependencies, and validation steps. Use this skill before implementing medium or large changes
---

# ---

name: implementation-planning

description: Plan software changes before modifying code by identifying affected components, implementation strategy, risks, dependencies, and validation steps. Use this skill before implementing medium or large changes.

---

# Implementation Planning

## Goal

Create a clear implementation plan before writing code.

A good implementation starts with a good plan.

The plan should minimize risk, reduce unnecessary changes, and fit naturally into the existing architecture.

---

# When to Use

Use this skill whenever:

- Implementing a new feature

- Fixing a non-trivial bug

- Refactoring multiple files

- Modifying shared components

- Updating APIs

- Changing database schemas

- Integrating external services

- Performing framework upgrades

Skip detailed planning only for trivial, isolated changes.

---

# Core Principles

- Think before editing.

- Reuse before creating.

- Minimize change surface.

- Respect existing architecture.

- Optimize for maintainability.

- Keep implementations incremental.

---

# Planning Workflow

## Step 1 — Understand the Existing System

Before proposing changes:

Identify:

- Current architecture

- Existing patterns

- Relevant modules

- Data flow

- Existing implementations

- Shared utilities

Never plan in isolation.

---

## Step 2 — Identify Affected Components

List every component that may change.

Examples:

- Controllers

- Services

- Repositories

- Entities

- DTOs

- Events

- Database schema

- API contracts

- Configuration

- Tests

- Documentation

Avoid discovering impacted files midway through implementation.

---

## Step 3 — Define the Implementation Strategy

Describe:

- Overall approach

- Why this approach is preferred

- Alternative approaches considered

- Trade-offs

Choose the simplest approach that satisfies the requirements.

---

## Step 4 — Break the Work into Steps

Prefer small, verifiable milestones.

Example:

1. Update domain model.

2. Add repository support.

3. Implement service logic.

4. Add API endpoint.

5. Add validation.

6. Add tests.

7. Update documentation.

Each step should leave the codebase in a working state.

---

## Step 5 — Identify Risks

Examples:

- Breaking API compatibility

- Database migration failures

- Performance regressions

- Race conditions

- Security implications

- Feature flag interactions

- Deployment sequencing

Describe how each risk will be mitigated.

---

## Step 6 — Define Validation

Specify how the implementation will be verified.

Include:

- Unit tests

- Integration tests

- Manual verification

- Performance checks

- API validation

- Database verification

---

## Step 7 — Rollback Strategy

If deployment fails:

Determine:

- What can be safely reverted?

- Are schema changes reversible?

- Are feature flags available?

- Will rollback require data migration?

Always consider recovery before implementation.

---

# Planning Guidelines

## Reuse Existing Patterns

Before creating:

- Controllers

- Services

- Utilities

- Events

- DTOs

- Configuration

Search the codebase for similar implementations.

Consistency is more valuable than originality.

---

## Prefer Incremental Changes

Avoid giant rewrites.

Instead:

- Add

- Verify

- Commit

Repeat.

Smaller changes are easier to review and debug.

---

## Minimize Blast Radius

Every modification has a cost.

Ask:

- Can fewer files change?

- Can existing abstractions be reused?

- Is this introducing unnecessary coupling?

- Is a new abstraction justified?

---

# Common Planning Mistakes

Avoid:

- Designing while coding

- Creating abstractions too early

- Refactoring unrelated code

- Ignoring deployment concerns

- Forgetting tests

- Ignoring backward compatibility

- Large, unreviewable changes

---

# Deliverable

Produce an implementation plan before coding.

## Summary

Brief description of the requested change.

## Components Affected

List the files, modules, or layers likely to change.

## Implementation Steps

Provide an ordered list of implementation tasks.

## Risks

Identify technical and deployment risks.

## Validation

Explain how correctness will be verified.

## Rollback

Describe how the change could be safely reverted if necessary.

---

# Guiding Principle

Code is the final step of implementation.

Planning is where most engineering decisions are made.

Write code only after the implementation approach is clear.