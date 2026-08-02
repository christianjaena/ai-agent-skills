---
name: aws-sqs-sns
description: Design and review messaging with AWS SQS and SNS covering queue configuration, visibility timeouts, DLQs, FIFO vs standard, fan-out patterns, idempotent consumers, and failure handling. Use this skill when building or reviewing asynchronous messaging, event fan-out, or diagnosing message loss, duplication, or DLQ growth.
---

# aws-sqs-sns

Design and review messaging with AWS SQS and SNS covering queue configuration, visibility timeouts, DLQs, FIFO vs standard, fan-out patterns, idempotent consumers, and failure handling.

## Goal

Build messaging that survives failures without losing or corrupting work: right topology, right queue settings, idempotent consumers, and dead-letter handling that gets acted on.

---

# When to Use

Use this skill whenever:

- Designing async communication between services
- Configuring SQS queues, SNS topics, or subscriptions
- Reviewing producer or consumer code
- Diagnosing duplicate processing, lost messages, or DLQ growth
- Choosing between SQS, SNS, EventBridge, or Kinesis

---

# Choosing the Tool

- SQS: point-to-point work queue; one logical consumer; buffering and load leveling.
- SNS: fan-out one event to many subscribers (queues, Lambdas, HTTP).
- SNS → SQS per consumer: the standard fan-out pattern; each consumer gets its own queue, retry policy, and DLQ.
- EventBridge: content-based routing, many event types, third-party/SaaS events, schema registry.
- Kinesis: ordered, replayable streams with high throughput and multiple readers of the same data.

Never subscribe a Lambda directly to SNS when you need retry buffering — put an SQS queue between them.

---

# Standard vs FIFO

- Standard: at-least-once delivery, best-effort ordering, effectively unlimited throughput. Default choice.
- FIFO: exactly-once processing (within the dedup window), strict ordering per `MessageGroupId`, limited throughput per group.
- Choose FIFO only when ordering or deduplication is a genuine business requirement; group IDs must be well-distributed or one hot group serializes everything.
- FIFO fan-out requires SNS FIFO with SQS FIFO subscribers.

---

# Consumer Correctness

At-least-once delivery means duplicates WILL happen. Every consumer must be idempotent:

- Dedup on message ID or business key with conditional writes (DynamoDB) or unique constraints.
- Delete the message only AFTER successful processing.
- Processing must complete within the visibility timeout, or the message reappears and runs twice concurrently — extend via heartbeat for long work.

Visibility timeout rules of thumb:

- ≥ max processing time with margin
- With Lambda: ≥ 6x the function timeout
- Long-running work: consider Step Functions instead of huge visibility timeouts

---

# Dead-Letter Queues

- Every queue gets a DLQ. No exceptions.
- `maxReceiveCount`: 3–5 typical; 1 hides transient failures, 100 delays discovery.
- Alarm on DLQ depth > 0; a DLQ nobody watches is a black hole.
- Have a redrive plan: SQS redrive-to-source after fixing the defect.
- DLQ retention: maximum (14 days) to allow investigation time.
- For SNS: configure subscription DLQs too — delivery failures to a broken subscriber are otherwise dropped after retries.

---

# Message Design

- Messages are contracts: version them, keep them backward compatible.
- Carry IDs and facts, not giant payloads; for large data, store in S3 and pass a reference (claim check pattern).
- Include an event ID, timestamp, and type for tracing and dedup.
- Don't put secrets or PII in messages unless encrypted and necessary.

---

# Producer Correctness

- Publishing after a DB commit is not atomic: a crash in between loses the event. When delivery must be guaranteed, use the transactional outbox pattern.
- Use batch APIs (`SendMessageBatch`, up to 10) for throughput; handle per-entry failures in the batch response.
- Enable SSE on queues/topics; enforce TLS-only access policies.

---

# Common Review Findings

- No DLQ, or DLQ without an alarm
- Non-idempotent consumers
- Visibility timeout shorter than processing time
- Delete-before-process (message loss on crash)
- FIFO chosen "for safety" without an ordering requirement
- Single hot `MessageGroupId` serializing a FIFO queue
- Publish inside a DB transaction assumed atomic
- Missing partial batch failure handling in Lambda consumers
- Broad `sqs:*` IAM permissions

---

# Testing

- Unit test handlers with duplicate and out-of-order message fixtures.
- Integration test against LocalStack or a dev account.
- Chaos-check: kill the consumer mid-processing and verify the message is reprocessed safely.
