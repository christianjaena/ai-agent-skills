---
name: aws-lambda
description: Write, review, and troubleshoot AWS Lambda functions covering handler design, cold starts, memory/timeout tuning, error handling, retries, idempotency, and packaging. Use this skill when building serverless functions, reviewing Lambda code, or diagnosing Lambda performance and failure behavior.
---

# aws-lambda

Write, review, and troubleshoot AWS Lambda functions covering handler design, cold starts, memory/timeout tuning, error handling, retries, idempotency, and packaging.

## Goal

Build Lambda functions that are fast to start, cheap to run, safe to retry, and easy to debug.

---

# When to Use

Use this skill whenever:

- Writing or reviewing Lambda handler code
- Diagnosing cold start latency or timeouts
- Configuring retries, DLQs, or event source mappings
- Tuning memory and cost
- Reviewing serverless deployment definitions (SAM, CDK, Serverless Framework, Terraform)

---

# Handler Design

- Keep handlers thin: parse event, call business logic, shape response. Business logic in plain testable classes/functions.
- Initialize SDK clients, DB connections, and config OUTSIDE the handler (executed once per environment, reused across warm invocations).
- Never store per-request state in globals — execution environments are reused.
- One function, one responsibility. Avoid "monolith Lambdas" routing dozens of operations, but don't create a function per trivial helper either.
- Design for statelessness: anything needed across invocations lives in a datastore, not memory.

---

# Cold Starts

Reduce them:

- Keep deployment packages small; trim dependencies aggressively.
- Prefer lighter runtimes and avoid heavyweight frameworks/DI containers for latency-sensitive functions.
- For JVM: consider SnapStart; for latency-critical paths: provisioned concurrency.
- Lazy-load rarely used dependencies.
- Keep VPC attachment only when needed (modern VPC networking is fast, but adds config surface).

Measure before optimizing: check `Init Duration` in CloudWatch logs / X-Ray.

---

# Errors, Retries, Idempotency

Retry behavior depends on the invocation type:

- Synchronous (API Gateway): no automatic retry — caller handles it.
- Asynchronous (S3, EventBridge, SNS): 2 retries by default, then to the failure destination/DLQ.
- Stream/queue (SQS, Kinesis, DynamoDB Streams): retried until success, expiry, or maxReceiveCount → DLQ.

Therefore:

- Every async/queue-triggered Lambda MUST be idempotent. Use an idempotency key (event ID, message ID) stored in DynamoDB with conditional writes, or Lambda Powertools idempotency.
- Always configure a DLQ or failure destination for async sources.
- For SQS batches: use partial batch responses (`ReportBatchItemFailures`) so one bad record doesn't recycle the whole batch.
- Let unexpected errors fail the invocation (so retry machinery works); don't catch-and-return-success.

---

# Configuration

- Memory sizing also scales CPU: profile with power tuning; more memory is often faster AND cheaper.
- Timeout: set to realistic p99 + margin, not the 15-minute max "to be safe" — long timeouts hide hangs and inflate costs on failure.
- Function timeout must be shorter than the SQS visibility timeout (rule of thumb: visibility timeout ≥ 6x function timeout for batches).
- Secrets from Secrets Manager/SSM (cached with Powertools or extension), not plaintext env vars for sensitive values.
- Reserved concurrency to protect downstream systems from Lambda scaling past their capacity.

---

# Observability

- Structured JSON logging with the request ID; log the event only at debug level (may contain PII).
- Emit business metrics via Embedded Metric Format or Powertools Metrics.
- Enable X-Ray tracing for functions in request chains.
- Alarm on: errors, throttles, DLQ depth, duration approaching timeout, iterator age (streams).

---

# Common Review Findings

- SDK clients created inside the handler
- No DLQ on async event sources
- Non-idempotent handlers on at-least-once event sources
- Missing partial batch failure handling for SQS
- Secrets in plaintext environment variables
- Timeout set to maximum without justification
- Lambda calling Lambda synchronously (couples scaling and doubles cost — use queues/step functions)
- Recursive invocation loops (S3 trigger writing to the same bucket/prefix)

---

# Testing

- Unit test business logic without any Lambda machinery.
- Test the handler with realistic event fixtures (API Gateway, SQS, S3 event shapes differ).
- Integration-test against LocalStack or a dev account for IAM/permission issues that mocks can't catch.
