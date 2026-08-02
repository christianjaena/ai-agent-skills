---
name: security-review
description: Review code changes for security risks including authentication, authorization, input validation, secrets management, data exposure, and OWASP vulnerabilities. Use this skill whenever implementing or reviewing backend features
---

# ---

name: security-review

description: Review code changes for security risks including authentication, authorization, input validation, secrets management, data exposure, and OWASP vulnerabilities. Use this skill whenever implementing or reviewing backend features.

---

# Security Review

## Goal

Identify and mitigate security risks before code reaches production.

Security is a feature, not an afterthought.

Review every change with the mindset:

> "If I were attacking this system, how could I abuse this?"

Never assume requests are trusted.

---

# When to Use

Use this skill whenever:

- Implementing new endpoints

- Reviewing pull requests

- Adding authentication

- Adding authorization

- Integrating third-party services

- Reading user input

- Uploading files

- Processing payments

- Handling personal or financial data

- Writing SQL

- Exposing APIs

---

# Security Principles

Follow these principles:

- Never trust client input.

- Validate everything.

- Authenticate first.

- Authorize every action.

- Fail securely.

- Least privilege.

- Secure by default.

---

# Authentication

Verify:

- Is the user authenticated?

- Can anonymous users access this?

- Is token validation correct?

- Are expired tokens rejected?

- Are sessions managed securely?

Never assume a user is authenticated because the frontend says so.

---

# Authorization

Check:

- Can the authenticated user perform this action?

- Can they access another user's data?

- Are role checks sufficient?

- Is ownership verified?

Authentication answers:

"Who are you?"

Authorization answers:

"What are you allowed to do?"

Every protected action requires authorization.

---

# Input Validation

Validate:

- Required fields

- Length

- Format

- Numeric ranges

- Enum values

- Dates

- File types

- File sizes

Reject invalid input early.

---

# SQL Safety

Never build SQL using string concatenation.

Prefer:

- Parameterized queries

- ORM query methods

- Prepared statements

Review custom queries carefully.

---

# Sensitive Data

Never log:

- Passwords

- Access tokens

- Refresh tokens

- API keys

- Credit card numbers

- CVVs

- Personal identification numbers

- Secrets

Mask or redact sensitive information where necessary.

---

# Secrets Management

Secrets should never be:

- Hardcoded

- Committed to source control

- Stored in configuration files

- Printed to logs

Use a secure secrets manager or environment variables.

---

# API Security

Verify:

- Authentication

- Authorization

- Rate limiting

- Input validation

- Error responses

Avoid exposing internal implementation details in error messages.

---

# File Uploads

Validate:

- File type

- MIME type

- Size

- Filename

- Virus scanning (if applicable)

Never trust the client-provided filename or content type.

---

# OWASP Top 10 Checklist

Review for:

- Broken Access Control

- Cryptographic Failures

- Injection

- Insecure Design

- Security Misconfiguration

- Vulnerable Components

- Identification and Authentication Failures

- Software and Data Integrity Failures

- Logging and Monitoring Failures

- Server-Side Request Forgery (SSRF)

---

# Dependency Review

Before introducing a dependency:

- Is it actively maintained?

- Does it have known vulnerabilities?

- Is there an existing dependency that already solves the problem?

Minimize unnecessary dependencies.

---

# Error Handling

Do not expose:

- Stack traces

- Database details

- Internal IDs

- Framework internals

- Server paths

Return safe, user-friendly error messages.

---

# Logging

Logs should contain:

- Timestamp

- Request ID

- User ID (where appropriate)

- Operation

- Outcome

Logs should never contain secrets.

---

# Common Mistakes

Avoid:

- Trusting client-provided IDs

- Missing authorization checks

- Overly permissive endpoints

- Logging secrets

- Hardcoded credentials

- Disabled TLS validation

- Returning sensitive data by default

- Ignoring dependency vulnerabilities

---

# Security Review Checklist

Before approving:

- Authentication verified

- Authorization verified

- Input validated

- SQL safe

- Secrets protected

- Sensitive logs avoided

- Dependencies reviewed

- Error handling secure

- APIs follow least privilege

- OWASP considerations addressed

---

# Deliverable

## Summary

Provide an overall security assessment.

## Findings

For each finding include:

- Severity (Critical, High, Medium, Low)

- Description

- Risk

- Recommendation

## Final Assessment

Choose one:

- Approved

- Approved with Recommendations

- Request Changes

---

# Guiding Principle

Working code is not necessarily secure code.

The best security issue is the one prevented during review instead of discovered in production.