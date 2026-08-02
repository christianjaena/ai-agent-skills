---
name: write-tests
description: Write high-quality unit, integration, and regression tests that verify behavior rather than implementation. Use this skill whenever adding features, fixing bugs, or refactoring code.
---

# write-tests

write-tests

description: Write high-quality unit, integration, and regression tests that verify behavior rather than implementation. Use this skill whenever adding features, fixing bugs, or refactoring code.

## Goal

Write tests that increase confidence in the software.

Tests should verify behavior, catch regressions, and remain easy to maintain.

Avoid writing tests solely to increase code coverage.

---

# When to Use

Use this skill whenever:

- Adding a new feature
- Fixing a bug
- Refactoring existing code
- Reviewing code without tests
- Updating business logic
- Changing API behavior

Every meaningful behavior change should be accompanied by tests.

---

# Testing Philosophy

Test observable behavior, not implementation details.

A good test answers:

> "If this behavior breaks in the future, will this test fail?"

Avoid coupling tests to internal implementation.

Refactoring should not require rewriting good tests.

---



# Testing Pyramid

Prefer:

```

Many Unit Tests

Some Integration Tests

Few End-to-End Tests

```

Unit tests should provide fast feedback.

Integration tests should verify component interaction.

End-to-end tests should validate critical user flows.

---



# Decide What to Test

Focus on:

- Business rules
- Calculations
- State transitions
- Validation
- Error handling
- Authorization
- Persistence
- API contracts

Do not waste time testing:

- Getters/setters
- Constructors without logic
- Framework behavior
- Library code
- Generated code

---



# Unit Testing

A unit test should isolate one class.

Mock external dependencies.

Do not mock the class under test.

Prefer constructor injection because it simplifies testing.

---



# Integration Testing

Verify:

- Spring configuration
- Repository behavior
- Transactions
- REST endpoints
- Database mappings
- Serialization

Use a real database when practical (for example, Testcontainers).

---



# Regression Tests

Every bug fix should include a test that reproduces the original bug.

The ideal workflow:

1. Write a failing test.
2. Fix the bug.
3. Verify the test now passes.

This prevents the same bug from returning.

---



# Test Structure

Use Arrange – Act – Assert.

Example:

Arrange:

- Create inputs
- Configure mocks

Act:

- Execute one action

Assert:

- Verify results

Keep each section obvious.

---



# Assertions

Assert meaningful outcomes.

Prefer:

- Returned values
- State changes
- Database updates
- Published events
- HTTP responses

Avoid asserting implementation details.

---



# Mockito Guidelines

Mock:

- External services
- Repositories (for unit tests)
- HTTP clients
- Message brokers

Do not mock:

- Value objects
- Collections
- Domain models
- Simple DTOs

Avoid excessive mocking.

---



# JUnit Guidelines

Use JUnit 5.

Prefer descriptive test names.

Example:

shouldRejectLoanWhenCreditScoreBelowMinimum()

Avoid names like:

test1()

---



# Edge Cases

Always consider:

- Null input
- Empty collections
- Invalid values
- Boundary values
- Duplicate data
- Maximum values
- Minimum values
- Time zones
- Dates
- Large datasets

---



# Error Cases

Verify:

- Exceptions
- Validation failures
- Unauthorized access
- Missing data
- Timeouts
- External service failures

---



# Spring Boot Testing

Prefer:

Unit Test

↓

Slice Test

↓

Integration Test

Avoid loading the entire Spring context unless necessary.

@SpringBootTest is powerful but expensive.

---



# Database Testing

Verify:

- Entity mappings
- Constraints
- Transactions
- Rollbacks
- Queries
- Index assumptions

Use realistic data.

---



# API Testing

Verify:

- Status codes
- Validation
- Error responses
- Serialization
- Authentication
- Authorization

Test both success and failure paths.

---



# Common Mistakes

Avoid:

- Testing private methods
- One assertion per line of code
- Massive setup methods
- Sleeping in tests
- Shared mutable test state
- Magic values
- Random failures (flaky tests)

Tests should be deterministic.

---



# Code Coverage

Coverage is a metric, not a goal.

100% coverage does not mean the software is correct.

A smaller suite of meaningful tests is more valuable than a large suite of trivial ones.

---



# Review Checklist

Before finishing:

- Does the test verify behavior?
- Is the test easy to understand?
- Can it fail for the right reason?
- Is the setup minimal?
- Are edge cases covered?
- Are error cases covered?
- Is the test deterministic?
- Does it avoid implementation details?

---



# Output Format

For every feature or bug fix:

1. Recommend the appropriate test type.
2. Explain why.
3. Generate the tests.
4. Explain what behaviors are verified.
5. Identify any missing edge cases.

---



# Guiding Principle

A good test gives developers confidence to change code.

A bad test gives developers fear of changing code.