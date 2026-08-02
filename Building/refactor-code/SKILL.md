---
name: refactor-code
description: Improve the structure, readability, maintainability, and quality of existing code while preserving behavior. Use this skill whenever cleaning up code or reducing technical debt
---

# ---

name: code improvement

description: Improve the structure, readability, maintainability, and quality of existing code while preserving behavior. Use this skill whenever cleaning up code or reducing technical debt.

---

# Code Improvement

## Goal

Improve the internal quality of the code without changing its observable behavior.

A successful refactoring makes the code easier to understand, extend, test, and maintain while producing the same functional result.

Behavior must remain unchanged.

---

# When to Use

Use this skill whenever:

- Cleaning up legacy code
- Reducing technical debt
- Improving readability
- Removing duplication
- Simplifying complex logic
- Preparing for a new feature
- Improving testability
- Improving maintainability

Do not use refactoring as an excuse to redesign unrelated parts of the system.

---



# Core Principles

- Preserve behavior.
- Make the smallest useful improvement.
- Improve one thing at a time.
- Follow existing project conventions.
- Refactor incrementally.

Refactoring is not rewriting.

---



# Before Refactoring

Understand:

- Why the code exists
- Current behavior
- Existing tests
- Business rules
- Dependencies

Never refactor code you do not understand.

---



# Refactoring Workflow



## Step 1 — Establish Safety

Before making changes:

- Review existing tests.
- Identify missing tests.
- Add regression tests if needed.
- Confirm current behavior.

Refactoring without safety nets is risky.

---



## Step 2 — Identify Code Smells

Look for:

- Duplicate code
- Long methods
- Long parameter lists
- Deep nesting
- Large classes
- Feature envy
- Primitive obsession
- God objects
- Shotgun surgery
- Dead code
- Unused abstractions

Not every smell needs immediate correction. Prioritize those that improve clarity and maintainability.

---



## Step 3 — Choose the Smallest Refactoring

Prefer:

- Extract Method
- Extract Class
- Rename Method
- Rename Variable
- Inline Variable
- Replace Magic Numbers with Constants
- Simplify Conditionals
- Remove Dead Code
- Introduce Value Objects
- Reduce Nesting with Early Returns

Avoid introducing new abstractions unless they clearly improve the design.

---



## Step 4 — Keep Changes Focused

Do not mix:

- Feature work
- Bug fixes
- Formatting changes
- Refactoring

Each commit should have a single purpose.

---



## Step 5 — Verify Behavior

After each refactoring:

- Run tests.
- Check compilation.
- Verify public behavior.
- Ensure APIs remain compatible.

If behavior changes, it is no longer a pure refactoring.

---



# Design Principles

Prefer:

- Single Responsibility Principle
- Composition over inheritance
- Immutable objects where practical
- Clear abstractions
- High cohesion
- Low coupling

Avoid unnecessary design patterns.

---



# Java Guidelines

Prefer:

- Constructor injection
- Records for immutable DTOs
- Optional where appropriate
- Small focused classes
- Early returns
- Descriptive names

Avoid:

- Static mutable state
- Deep inheritance hierarchies
- Excessive Streams when loops are clearer
- Clever code that reduces readability

---



# Spring Boot Guidelines

Keep:

- Controllers thin
- Services focused on business logic
- Repositories limited to persistence
- Validation centralized
- Exception handling consistent

Avoid moving business logic into controllers or repositories.

---



# Common Refactorings



## Simplify Complex Conditionals

Replace nested if statements with:

- Guard clauses
- Polymorphism
- Strategy pattern (only if justified)

---



## Remove Duplication

Extract shared logic into reusable methods or services.

Do not duplicate business rules.

---



## Improve Naming

Names should explain intent.

Bad:

calculate()

Good:

calculateOutstandingLoanBalance()

---



## Reduce Method Size

Aim for methods that perform one logical task.

Break long methods into smaller, well-named units.

---



## Improve Testability

Reduce hidden dependencies.

Inject collaborators instead of creating them internally.

Separate business logic from framework code.

---



# What Not to Refactor

Avoid changing:

- Stable, well-understood code without a reason
- Public contracts
- Working code during critical production incidents
- Large areas unrelated to the requested task

Do not refactor "just because."

---



# Review Checklist

Before finishing:

- Behavior unchanged
- Existing tests still pass
- New tests added if necessary
- Complexity reduced
- Readability improved
- Duplication reduced
- Naming improved
- No unnecessary abstractions introduced

---



# Deliverable



## Summary

Describe what was improved.

## Refactorings Applied

List each refactoring performed.

## Benefits

Explain how maintainability, readability, or testability improved.

## Risks

Identify any areas that deserve extra review.

---



# Guiding Principle

The best refactoring is one that future developers notice only because the code is easier to understand—not because it looks completely different.

Every refactoring should leave the codebase slightly better than it was before.