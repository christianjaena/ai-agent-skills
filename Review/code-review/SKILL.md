---
name: code-review
description: Review code changes for correctness, maintainability, performance, security, and architectural consistency. Use this skill whenever reviewing pull requests, AI-generated code, or significant code modifications.
---

# code-review

code-review

Review code changes for correctness, maintainability, performance, security, and architectural consistency. Use this skill whenever reviewing pull requests, AI-generated code, or significant code modifications.



## Goal

Perform a senior-level code review that prioritizes correctness, maintainability, readability, security, and long-term health of the codebase.

Do not simply look for syntax errors. Evaluate whether the change is the right solution for the problem.

---

# When to Use

Use this skill whenever:

- Reviewing pull requests
- Reviewing AI-generated code
- Reviewing refactoring
- Reviewing bug fixes
- Reviewing new features
- Reviewing production fixes
- Reviewing infrastructure changes
- Reviewing tests
- Reviewing database migrations

---

# Review Philosophy

Think like an owner of the codebase.

Assume this code will be maintained for the next five years.

Prefer simplicity over cleverness.

Optimize for future developers.

Every review comment should improve one of these:

- Correctness
- Reliability
- Maintainability
- Readability
- Performance
- Security
- Testability

Avoid personal preference comments unless consistency is important.

---



# Review Workflow



## 1. Understand the Change

Before reviewing:

- Read the problem statement.
- Understand the feature or bug.
- Determine the intended behavior.
- Identify the affected components.
- Avoid reviewing line-by-line before understanding the overall change.

Never review code without understanding its purpose.

---



## 2. Architectural Review

Ask:

- Does this belong in this layer?
- Is business logic placed correctly?
- Does it violate Clean Architecture?
- Does it introduce unnecessary coupling?
- Does it duplicate existing functionality?
- Is a new abstraction actually needed?

Prefer extending existing abstractions over creating new ones without clear value.

---



## 3. Correctness

Verify:

- Edge cases
- Null handling
- Empty collections
- Invalid input
- Error propagation
- Exception handling
- Resource cleanup

Look for:

- Hidden bugs
- Incorrect assumptions
- Off-by-one errors
- Race conditions
- Incorrect comparisons
- Missing validation

Never assume generated code is correct.

---



## 4. Readability

Good code explains itself.

Prefer:

- Small methods
- Descriptive names
- Intent-revealing variables
- Early returns
- Low nesting
- Consistent formatting

Avoid:

- Clever one-liners
- Magic numbers
- Abbreviations
- Unnecessary comments
- Deep nesting

---



## 5. Maintainability

Check for:

- Single Responsibility Principle
- DRY
- SOLID
- Reusable logic
- Consistent abstractions

Avoid:

- God classes
- Long methods
- Duplicate logic
- Feature envy
- Shotgun surgery

---



## 6. Performance

Ask:

- Is this algorithm appropriate?
- Is there unnecessary looping?
- Is the database queried efficiently?
- Can expensive work be cached?
- Is lazy loading causing N+1 queries?

Don't optimize prematurely, but identify obvious inefficiencies.

---



## 7. Security

Check for:

- SQL Injection
- Command Injection
- Path Traversal
- Unsafe deserialization
- XSS (where applicable)
- Missing authorization
- Missing authentication
- Secrets in code
- Sensitive data in logs

Validate all external input.

Never trust client-provided values.

---



## 8. Concurrency

For multithreaded code:

Review:

- Synchronization
- Atomicity
- Locks
- Shared mutable state
- Thread safety
- Race conditions

Pay special attention to async workflows.

---



## 9. Database

Review:

- Transactions
- Rollbacks
- Isolation
- Index usage
- Query count
- N+1 queries
- Migration safety

Schema changes should be backwards compatible whenever possible.

---



## 10. API Design

Check:

- REST conventions
- Status codes
- Error messages
- Validation
- Versioning
- Naming consistency

Public APIs should be stable.

---



## 11. Testing

Every meaningful behavior change should have tests.

Review:

- Happy path
- Failure path
- Edge cases
- Boundary conditions

Avoid:

- Testing implementation details
- Brittle mocks
- Low-value assertions

Prefer behavior-focused tests.

---



## 12. Logging

Logs should help production debugging.

Avoid:

- Logging passwords
- Logging tokens
- Logging PII
- Duplicate logs
- Log spam

Include enough context to diagnose failures.

---



## 13. Documentation

Verify:

- Public APIs documented
- Breaking changes documented
- Configuration changes documented
- Migration steps documented

---



# Java Review Checklist

- Uses Optional appropriately
- Avoids null where possible
- Immutable where practical
- Uses records when appropriate
- Uses Streams only when readable
- Correct equals/hashCode
- Proper exception hierarchy
- Proper generics usage
- Constructor injection
- No unnecessary static state

---



# Spring Boot Checklist

- Thin controllers
- Business logic in services
- Repository only for persistence
- Validation using Bean Validation
- Global exception handling
- Transaction boundaries are correct
- Dependency injection through constructors
- Configuration externalized

---



# Things to Praise

Don't only find problems.

Highlight:

- Elegant solutions
- Good abstractions
- Clear naming
- Well-written tests
- Good documentation
- Performance improvements
- Security improvements

Positive reinforcement encourages good engineering practices.

---



# Review Output Format

Structure reviews as:

## Summary

One paragraph describing overall quality.

## Strengths

- ...



## Issues

For each issue include:

Severity:

- Critical
- High
- Medium
- Low
- Suggestion

Include:

- Why it's an issue
- Recommended fix
- Expected impact



## Final Recommendation

One of:

- Approve
- Approve with minor suggestions
- Request changes
- Needs architectural discussion

---



# Guiding Principle

The goal of a review is **not to prove the author wrong**.

The goal is to improve the codebase while helping the author grow.