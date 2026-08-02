---
name: change-impact-analysis
description: Analyze the impact of proposed code changes by identifying affected components, dependencies, compatibility risks, and potential regressions before implementation
---

# ---

name: change-impact-analysis

description: Analyze the impact of proposed code changes by identifying affected components, dependencies, compatibility risks, and potential regressions before implementation.

---

# Change Impact Analysis

## Goal

Understand the full consequences of a change before modifying code.

A seemingly small change can have wide-reaching effects across APIs, databases, events, integrations, tests, and deployments.

The objective is to minimize regressions and avoid unexpected side effects.

---

# When to Use

Use this skill whenever:

- Modifying existing business logic

- Changing public APIs

- Refactoring shared components

- Updating database schemas

- Changing domain models

- Modifying event payloads

- Replacing dependencies

- Performing framework upgrades

- Removing or renaming classes

- Changing configuration behavior

---

# Core Principles

- Every change has downstream effects.

- Shared code requires extra caution.

- Public contracts should remain stable.

- Favor backward compatibility.

- Identify risks before implementation.

---

# Analysis Workflow

## Step 1 — Identify the Change

Summarize:

- What is changing?

- Why is it changing?

- Is it behavioral, structural, or both?

---

## Step 2 — Identify Direct Dependencies

Locate components that directly use the code.

Examples:

- Controllers

- Services

- Repositories

- Scheduled jobs

- Event publishers

- Event consumers

- Configuration classes

- Utilities

Search for all usages before modifying shared code.

---

## Step 3 — Identify Indirect Dependencies

Consider downstream effects.

Examples:

- External APIs

- Mobile applications

- Frontend clients

- Other microservices

- Reporting jobs

- Batch processes

- Data pipelines

- Monitoring dashboards

Indirect consumers are often the source of production regressions.

---

## Step 4 — Analyze Data Flow

Trace the complete flow.

Example:

HTTP Request

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Event Publisher

↓

Consumer

↓

Notification

Understand where the change propagates.

---

## Step 5 — Evaluate Backward Compatibility

Ask:

- Will existing clients continue to work?

- Are response formats changing?

- Are request contracts changing?

- Are database migrations additive?

- Are events still compatible?

Prefer additive changes over breaking changes.

---

## Step 6 — Identify Required Updates

Determine what must also change.

Possible updates:

- Tests

- Documentation

- OpenAPI

- Database migrations

- Monitoring

- Feature flags

- Configuration

- Deployment scripts

- Consumer services

---

## Step 7 — Risk Assessment

Evaluate:

### High Risk

- Public API changes

- Database schema removal

- Event contract changes

- Authentication changes

- Transaction changes

### Medium Risk

- Shared utility modifications

- Service refactoring

- Validation updates

- Performance-sensitive code

### Low Risk

- Internal implementation changes

- Logging improvements

- Documentation updates

- Test-only changes

---

# Things to Check

## API

- Request objects

- Response objects

- Status codes

- Validation

- Clients

---

## Database

- Migrations

- Indexes

- Constraints

- Existing data

- Rollback strategy

---

## Events

- Event schema

- Consumers

- Ordering

- Retry behavior

- Idempotency

---

## Security

- Authentication

- Authorization

- Permissions

- Secrets

- Audit logging

---

## Performance

- Query count

- Caching

- Thread usage

- Memory allocation

- Network calls

---

## Testing

Verify whether existing tests still cover:

- Happy path

- Error handling

- Edge cases

- Integration behavior

- Regression scenarios

Add new tests where change introduces new behavior.

---

# Questions to Ask

Before implementing:

- Which modules depend on this?

- Is this part of a public contract?

- Can existing consumers break?

- Is a feature flag needed?

- Is the deployment order important?

- Is rollback straightforward?

- Should this change be incremental?

---

# Deliverable

## Change Summary

Describe the intended modification.

## Direct Impact

List directly affected components.

## Indirect Impact

List downstream systems or consumers.

## Compatibility

Identify any breaking changes.

## Risks

Rank risks by severity.

## Required Updates

List tests, documentation, configuration, monitoring, or migrations that must also change.

## Recommendation

State whether the change is:

- Safe

- Low Risk

- Medium Risk

- High Risk

Explain why.

---

# Guiding Principle

A good engineer knows how to make a change.

A great engineer knows everything that change will affect before writing a single line of code.