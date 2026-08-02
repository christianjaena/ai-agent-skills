---
name: implement-feature
description: Implement new backend features by understanding requirements, following the existing architecture, minimizing unnecessary changes, and delivering production-ready code with tests and documentation.
---

# implement-feature

---

name: implement-feature

description: Implement new backend features by understanding requirements, following the existing architecture, minimizing unnecessary changes, and delivering production-ready code with tests and documentation.

---

# Implement Feature

## Goal

Implement new functionality that integrates naturally with the existing codebase.

Prioritize correctness, maintainability, and consistency over speed.

The implementation should look as if it was written by the original team.

---

# When to Use

Use this skill whenever:

- Adding a new feature
- Implementing a user story
- Creating a new endpoint
- Adding business logic
- Adding scheduled jobs
- Implementing integrations
- Creating repositories
- Creating services
- Creating controllers

---



# Engineering Philosophy

Do not start coding immediately.

Spend more time understanding the existing system than writing code.

The best implementation is usually the one that introduces the fewest changes.

Avoid rewriting existing code unless necessary.

---



# Implementation Workflow



## Step 1 — Understand the Request

Identify:

- Business objective
- Inputs
- Outputs
- Constraints
- Edge cases
- Acceptance criteria

If requirements are ambiguous, ask clarifying questions before implementing.

Never invent business rules.

---



## Step 2 — Explore the Codebase

Before creating anything:

Find:

- Similar endpoints
- Similar services
- Similar repositories
- Existing utilities
- Existing DTOs
- Existing validation
- Existing exception handling
- Existing events

Reuse existing patterns.

Do not create a new pattern if an established one already exists.

---



## Step 3 — Plan the Change

Describe:

- Files to modify
- Files to create
- Data flow
- Dependencies
- Risks

Think before editing.

---



## Step 4 — Keep the Architecture Consistent

Follow the existing architecture.

Typical Spring structure:

Controller

↓

Service

↓

Repository

↓

Database

Business logic belongs in services.

Controllers should coordinate requests, not implement business rules.

Repositories should only perform persistence.

---



## Step 5 — Implement Incrementally

Prefer several small commits over one massive change.

After each logical change:

- Verify compilation.
- Verify tests.
- Verify behavior.

Avoid implementing everything at once.

---



# Coding Guidelines



## Java

Prefer:

- Constructor injection
- Records for immutable DTOs
- Optional when appropriate
- Early returns
- Small methods
- Meaningful names

Avoid:

- Static mutable state
- Deep nesting
- Long methods
- Large switch statements
- Duplicate logic

---



## Spring Boot

Use:

- Bean Validation
- Global exception handling
- Transaction boundaries
- Dependency Injection
- Configuration Properties

Avoid:

- Business logic in controllers
- Manual object creation
- Field injection
- Catching generic Exception
- Hidden transactions

---



## REST API

Follow REST conventions.

Resources should be nouns.

Return meaningful HTTP status codes.

Validate requests.

Provide useful error messages.

Avoid breaking existing contracts.

---



## Persistence

Review:

- Entity relationships
- Transactions
- Existing repositories
- Query efficiency

Avoid N+1 queries.

Do not introduce unnecessary custom queries.

---



## Error Handling

Fail fast.

Throw meaningful exceptions.

Surface user-friendly API errors.

Do not swallow exceptions.

---



## Logging

Log:

- Business-significant events
- External service failures
- Unexpected conditions

Do not log:

- Passwords
- Tokens
- Personal data
- Duplicate information

---



# Testing

Every feature should include tests.

Minimum expectations:

- Happy path
- Validation failures
- Error handling
- Edge cases

Choose the appropriate test type:

- Unit
- Integration
- Contract

---



# Backward Compatibility

Consider:

- Existing clients
- Existing APIs
- Existing events
- Existing database schema

Avoid breaking changes unless explicitly requested.

---



# Performance

Ask:

- Does this introduce unnecessary queries?
- Is caching appropriate?
- Can work be deferred?
- Is pagination required?
- Is the algorithm efficient?

Do not optimize prematurely.

---



# Security

Verify:

- Authentication
- Authorization
- Input validation
- Output sanitization
- Secret handling

Never trust external input.

---



# Documentation

If applicable:

- Update OpenAPI
- Update README
- Document configuration
- Document migrations

---



# Before Finishing

Review:

- Existing conventions followed
- Naming consistency
- Error handling
- Tests
- Logging
- Security
- Performance
- Maintainability

---



# Output Format

Always provide:

## Summary

What was implemented.

## Files Changed

List created and modified files.

## Design Decisions

Explain important implementation choices.

## Testing

Describe the tests added or updated.

## Risks

Mention any assumptions, limitations, or follow-up work.

---



# Guiding Principle

The best feature implementation is one that feels like it was always part of the project.

Future developers should not be able to tell which code was newly added.