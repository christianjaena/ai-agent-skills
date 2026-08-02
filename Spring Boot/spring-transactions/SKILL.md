---
name: spring-transactions
description: Design and review Spring transaction management including @Transactional boundaries, propagation, isolation, rollback rules, and self-invocation pitfalls. Use this skill when placing transaction boundaries, debugging transactions that don't roll back, or reviewing code that mixes transactions with external calls, events, or async work.
---

# spring-transactions

Design and review Spring transaction management including @Transactional boundaries, propagation, isolation, rollback rules, and self-invocation pitfalls.

## Goal

Place transaction boundaries deliberately so data stays consistent, locks stay short, and rollback behavior matches expectations.

---

# When to Use

Use this skill whenever:

- Adding or moving `@Transactional` annotations
- Debugging changes that don't roll back or partially commit
- Reviewing service methods that mix DB writes with external calls
- Working with propagation levels, isolation levels, or programmatic transactions
- Combining transactions with events, async methods, or messaging

---

# Boundary Placement

- Transaction boundary belongs at the service layer, on the use-case method.
- Not on controllers (ties DB transaction to HTTP handling).
- Not on repositories individually when a use case spans multiple writes.
- One use case = one transaction. If a method commits halfway through a business operation, the boundary is wrong.
- Keep transactions short: no remote HTTP calls, no message publishing, no file I/O, no sleeps inside a transaction.

---

# How Spring Transactions Actually Work

`@Transactional` works via proxies. Consequences:

- Self-invocation bypasses the proxy: `this.otherTransactionalMethod()` runs WITHOUT the advertised transaction semantics. Extract to another bean or restructure.
- Only public methods are transactional with proxy mode (default).
- The proxy is on the bean, so calls must come from outside the bean.

This is the single most common transaction bug in Spring codebases. Check for it in every review.

---

# Rollback Rules

- Default: rollback on unchecked exceptions (`RuntimeException`, `Error`) only. Checked exceptions COMMIT by default.
- Use `rollbackFor = Exception.class` when checked exceptions must roll back, or prefer unchecked exceptions.
- Catching an exception inside the transactional method prevents rollback — but if a participating inner transaction already marked rollback-only, the commit throws `UnexpectedRollbackException`.
- Never swallow exceptions inside transactions "to continue processing" without understanding rollback-only marking.

---

# Propagation

| Propagation | Use when |
|---|---|
| `REQUIRED` (default) | Almost always correct |
| `REQUIRES_NEW` | Independent commit needed (audit logs, outbox writes that must survive rollback) — note: suspends outer tx, uses a second connection |
| `MANDATORY` | Enforce that a caller owns the transaction |
| `NESTED` | Savepoint-based partial rollback (JDBC only) |
| `NOT_SUPPORTED` | Suspend transaction for long non-DB work |
| `NEVER` / `SUPPORTS` | Rare; assertion-style semantics |

`REQUIRES_NEW` inside a loop is a red flag: connection pool exhaustion risk (outer + inner connections held simultaneously).

---

# Isolation and Locking

- Default isolation (usually READ_COMMITTED) is right for most workloads.
- Lost updates: prefer optimistic locking (`@Version`) over raising isolation.
- Pessimistic locks (`@Lock(PESSIMISTIC_WRITE)`) only for genuine contention hotspots; always with a lock timeout.
- SERIALIZABLE: last resort; expect retries on serialization failures.

---

# Transactions + Other Mechanisms

- `@Async`: the async method runs in a different thread — it does NOT participate in the caller's transaction.
- Events: use `@TransactionalEventListener(phase = AFTER_COMMIT)` for side effects that must only happen on commit.
- Messaging/external calls: publish after commit, or use the transactional outbox pattern for atomicity.
- `readOnly = true` for query-only methods: enables driver/ORM optimizations and documents intent.

---

# Common Review Findings

- Self-invocation of `@Transactional` methods
- HTTP/messaging calls inside transactions
- Checked exceptions expected to roll back but committing
- `@Transactional` on private methods (silently ignored)
- Missing transaction around multi-write use cases
- `REQUIRES_NEW` used to "fix" rollback issues without understanding them
- Event listeners doing DB writes without their own transaction after `AFTER_COMMIT`

---

# Testing

- Integration-test rollback behavior explicitly: force the exception, assert nothing persisted.
- Beware `@Transactional` on tests: it rolls back everything and can hide flush/constraint bugs — test commit paths with explicit commits (e.g. `TestTransaction` utilities) where correctness matters.
