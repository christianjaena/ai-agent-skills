---
name: design-rest-api
description: Design and review REST APIs that are consistent, secure, scalable, intuitive, and maintainable. Use this skill whenever creating, modifying, or reviewing HTTP APIs.
---

---

name: design-rest-api

description: Design and review REST APIs that are consistent, secure, scalable, intuitive, and maintainable. Use this skill whenever creating, modifying, or reviewing HTTP APIs.

---

# Design REST API

## Goal

Design REST APIs that are intuitive, consistent, secure, and easy to evolve.

A well-designed API minimizes client confusion, supports future growth, and follows HTTP semantics correctly.

APIs are contracts.

Treat breaking changes with extreme caution.

---

# When to Use

Use this skill whenever:

- Designing new endpoints

- Modifying existing APIs

- Reviewing pull requests

- Versioning APIs

- Designing DTOs

- Integrating frontend applications

- Building public APIs

- Building internal APIs

---

# Core Principles

- Resources over actions

- Consistency over creativity

- Backward compatibility whenever possible

- Clear contracts

- Proper HTTP semantics

- Explicit validation

- Predictable error handling

---

# API Design Workflow

## Step 1 — Identify Resources

Model nouns, not verbs.

Good:

/users

/orders

/payments

/invoices

Bad:

/getUsers

/createOrder

/deleteLoan

REST models resources.

HTTP methods represent actions.

---

## Step 2 — Choose HTTP Methods

GET

Retrieve data.

Must be safe and idempotent.

POST

Create resources.

Usually not idempotent.

PUT

Replace an entire resource.

Must be idempotent.

PATCH

Partially update a resource.

Prefer PATCH when only some fields change.

DELETE

Remove a resource.

Should be idempotent.

---

## Step 3 — Design URIs

Prefer:

/users/{id}

/users/{id}/orders

/orders/{orderId}

/payments

Avoid:

/userList

/getAllUsers

/doPayment

/createOrderNow

URIs should represent resources.

---

## Step 4 — Design Request DTOs

Include only fields the client should provide.

Separate:

Create DTO

Update DTO

Response DTO

Avoid exposing internal entities directly.

---

## Step 5 — Design Response DTOs

Return only necessary information.

Avoid:

- Internal IDs

- Sensitive fields

- Debug information

- Database implementation details

Keep responses stable.

---

# Status Codes

Use appropriate HTTP status codes.

200 OK

Successful retrieval or update.

201 Created

Resource successfully created.

202 Accepted

Accepted for asynchronous processing.

204 No Content

Successful operation with no response body.

400 Bad Request

Invalid request format.

401 Unauthorized

Authentication required.

403 Forbidden

Authenticated but not permitted.

404 Not Found

Resource does not exist.

409 Conflict

Conflict with current state.

422 Unprocessable Entity

Validation failed.

429 Too Many Requests

Rate limit exceeded.

500 Internal Server Error

Unexpected server error.

Avoid returning 200 for failures.

---

# Validation

Validate:

- Required fields

- Length

- Format

- Enum values

- Numeric ranges

- Dates

- Business rules

Return meaningful validation errors.

---

# Error Responses

Errors should be consistent.

Include:

- Timestamp

- Status

- Error code

- Message

- Path

- Correlation ID (if available)

Prefer RFC 9457 Problem Details when practical.

Avoid exposing stack traces.

---

# Pagination

For collections:

Support:

- page

- size

or

- limit

- offset

Return:

- total items

- current page

- page size

- total pages

Never return massive datasets by default.

---

# Filtering

Support filtering through query parameters.

Example:

GET /users?status=ACTIVE

GET /orders?customerId=123

Prefer explicit filters.

---

# Sorting

Allow sorting.

Example:

sort=name

sort=createdAt,desc

Document supported fields.

---

# Searching

Separate searching from filtering.

Example:

GET /products?search=laptop

Keep search semantics predictable.

---

# Versioning

Prefer stable APIs.

When breaking changes are unavoidable:

- URI versioning (e.g. /v1/users)

- Header versioning (if established in your organization)

Avoid unnecessary versions.

---

# Idempotency

Ensure operations behave correctly when retried.

Examples:

PUT

DELETE

Payment APIs

Webhook handlers

Support idempotency keys where duplicate requests are possible.

---

# Security

Review:

- Authentication

- Authorization

- Input validation

- Rate limiting

- Sensitive data exposure

- CORS configuration

Never trust client input.

---

# Performance

Avoid:

- Returning unnecessary fields

- N+1 database queries

- Excessive nested resources

Support pagination for large collections.

---

# Documentation

Every endpoint should document:

- Purpose

- Request body

- Response body

- Status codes

- Validation rules

- Authentication requirements

- Example requests

- Example responses

Prefer OpenAPI.

---

# Spring Boot Guidelines

Controllers should:

- Validate requests

- Delegate business logic to services

- Return DTOs

- Handle HTTP concerns only

Services should contain business logic.

Repositories should handle persistence only.

---

# Common Mistakes

Avoid:

- Verbs in URIs

- Returning entities directly

- Inconsistent status codes

- Missing validation

- Breaking API contracts

- Leaking internal models

- Returning excessive data

- Ignoring idempotency

- Inconsistent error formats

---

# Review Checklist

Verify:

- Resource-oriented URIs

- Correct HTTP methods

- Appropriate status codes

- DTO separation

- Validation

- Consistent error handling

- Pagination

- Filtering

- Versioning strategy

- Authentication

- Authorization

- Documentation

---

# Deliverable

## API Summary

Describe the purpose of the API.

## Endpoints

List endpoints with methods.

## Request Models

Describe request DTOs.

## Response Models

Describe response DTOs.

## Validation Rules

Summarize input validation.

## Error Handling

Document error responses.

## Security Review

Authentication, authorization, and data exposure.

## Recommendations

Provide prioritized improvements.

---

# Guiding Principle

An API should feel predictable.

If developers need to guess how an endpoint behaves, the design needs improvement.

The best REST APIs are boring—they follow conventions consistently, making them easy to learn, use, and maintain.