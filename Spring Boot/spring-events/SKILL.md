---
name: spring-events
description: Design and review Spring application events including ApplicationEventPublisher, @EventListener, @TransactionalEventListener, async listeners, and when to use events versus direct calls or messaging. Use this skill when decoupling components with domain events or reviewing event-driven Spring code.
---

# spring-events

Design and review Spring application events including ApplicationEventPublisher, @EventListener, @TransactionalEventListener, async listeners, and when to use events versus direct calls or messaging.

## Goal

Use in-process events to decouple side effects from core use cases without losing consistency, ordering, or debuggability.

---

# When to Use

Use this skill whenever:

- Publishing domain events with `ApplicationEventPublisher`
- Writing `@EventListener` or `@TransactionalEventListener` methods
- Deciding between events, direct service calls, and a message broker
- Reviewing event-driven Spring code
- Debugging missing or duplicated event side effects

---

# When Events Are the Right Tool

Use Spring events when:

- A use case has secondary side effects that shouldn't couple to the caller (notifications, cache invalidation, statistics)
- Multiple independent reactions to one domain occurrence
- Breaking a circular dependency between services

Do NOT use events when:

- The caller needs the result — use a direct call
- The work must survive process crashes — use a message broker or outbox; Spring events are in-memory and lost on crash
- Consumers live in other services — use real messaging

Events model facts that happened ("OrderPlaced"), not commands ("SendEmail"). Name them in past tense.

---

# Event Design

- Events are immutable: use records.
- Carry IDs and essential data, not live entities or huge object graphs.
- Plain objects; extending `ApplicationEvent` is unnecessary.
- Keep one event type per business fact, not generic `EntityChangedEvent` with type flags.

---

# Listener Semantics

Defaults everyone forgets:

- `@EventListener` runs SYNCHRONOUSLY on the publisher's thread, inside the publisher's transaction if one is active.
- A listener exception propagates to the publisher and rolls back the transaction.
- Multiple listeners run in unspecified order unless `@Order` is set.

Consequences:

- Slow listeners make the use case slow.
- A "non-critical" listener failing breaks the main operation unless made async or exception-isolated.

---

# Transactional Event Listeners

`@TransactionalEventListener` binds execution to a transaction phase:

- `AFTER_COMMIT` (default): the right choice for side effects that must only happen when the data is actually saved (emails, external calls, cache invalidation).
- `BEFORE_COMMIT`: validations or same-transaction writes.
- `AFTER_ROLLBACK` / `AFTER_COMPLETION`: cleanup paths.

Critical gotchas:

- `AFTER_COMMIT` listeners run in the original transaction's afterCommit hook: DB writes inside them silently DO NOTHING unless annotated `@Transactional(propagation = REQUIRES_NEW)`.
- If no transaction is active, `@TransactionalEventListener` does not fire by default (`fallbackExecution = true` to change).
- AFTER_COMMIT is not "guaranteed delivery": a crash between commit and listener execution loses the event. Use the transactional outbox pattern when delivery is required.

---

# Async Listeners

- `@Async @EventListener` frees the publisher thread; requires `@EnableAsync` and a configured executor.
- Async listener exceptions vanish into the `AsyncUncaughtExceptionHandler` — configure it and add logging.
- Async + AFTER_COMMIT is the common combination for external side effects.
- Async listeners lose the caller's security context, MDC, and transaction — propagate explicitly if needed.

---

# Common Review Findings

- External API calls in synchronous listeners inside transactions
- DB writes in `AFTER_COMMIT` listeners without `REQUIRES_NEW`
- Business-critical flows split across events, making the use case impossible to follow
- Missing `@EnableAsync` so `@Async` silently runs synchronously
- Listener ordering assumptions without `@Order`
- Events used where guaranteed delivery is required (should be outbox + broker)

---

# Testing

- Unit test listeners as plain methods with the event object.
- `@RecordApplicationEvents` to assert events were published.
- Integration-test transactional phase behavior: assert AFTER_COMMIT side effects don't run on rollback.
- For async listeners, use Awaitility rather than sleeps.
