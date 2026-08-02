---
name: spring-data-jpa
description: Write and review Spring Data JPA code covering entity mapping, repositories, query performance, N+1 prevention, pagination, and projections. Use this skill when working with JPA entities, Hibernate, Spring Data repositories, or diagnosing ORM performance problems.
---

# spring-data-jpa

Write and review Spring Data JPA code covering entity mapping, repositories, query performance, N+1 prevention, pagination, and projections.

## Goal

Use JPA productively without the classic traps: hidden N+1 queries, entity leakage, broken equality, and accidental full-table loads.

---

# When to Use

Use this skill whenever:

- Designing or modifying JPA entities
- Writing Spring Data repositories or custom queries
- Diagnosing slow database access or N+1 problems
- Reviewing persistence-layer changes
- Adding pagination, projections, or specifications

---

# Entity Mapping

- Keep entities persistence-focused; never expose them through REST APIs. Map to DTOs.
- Default all associations to `FetchType.LAZY`, including `@ManyToOne` and `@OneToOne` (which are EAGER by default).
- `equals`/`hashCode`: base on the ID with null-safety, or a natural business key. Never include mutable fields or collections.
- Prefer generated identifiers (`SEQUENCE` over `IDENTITY` for batch insert support).
- Avoid bidirectional associations unless genuinely needed; if used, maintain both sides in a helper method.
- Avoid `CascadeType.ALL` with `orphanRemoval` unless the child's lifecycle truly belongs to the parent.
- Use `@Version` for optimistic locking on concurrently edited entities.

---

# Repositories

- Derived query methods for simple lookups; `@Query` (JPQL) when derivation gets unreadable.
- Native queries only when JPQL cannot express it; document why.
- Never return `List<Entity>` for unbounded data — use `Page`, `Slice`, or a `Stream` with a limit.
- Modifying queries: `@Modifying` + `@Transactional`, and remember they bypass the persistence context and lifecycle callbacks.
- Keep repositories per aggregate root, not per table reflexively.

---

# N+1 Prevention

The most common JPA performance failure. Watch for lazy collections accessed in loops or during serialization.

Fixes in preference order:

1. `@EntityGraph` on the repository method
2. `JOIN FETCH` in JPQL
3. DTO projection that selects exactly what's needed
4. `@BatchSize` / `hibernate.default_batch_fetch_size` as a global mitigation

Never fix N+1 by switching to EAGER fetching — that makes every query pay the cost.

Verify with SQL logging in tests: assert query counts for hot paths.

---

# Projections and DTOs

- Read-heavy endpoints: prefer interface or record projections over loading full entities.
- Constructor expressions in JPQL (`select new com.example.MyDto(...)`) or Spring Data projections.
- Don't load entities just to copy them into DTOs when a projection query works.

---

# Pagination and Sorting

- Accept `Pageable` at the API boundary with a maximum page size cap.
- `JOIN FETCH` on collections combined with pagination causes in-memory pagination — avoid; paginate IDs first or use `@BatchSize`.
- Use `Slice` when total count is unnecessary (count queries can be expensive).

---

# Transactions and the Persistence Context

- Read-only queries: `@Transactional(readOnly = true)` enables optimizations and skips dirty checking.
- Dirty checking means managed entities are auto-flushed — you rarely need to call `save()` on a managed entity.
- Avoid long transactions holding the persistence context over remote calls.
- Open Session in View: disable it (`spring.jpa.open-in-view=false`) and fetch what the view needs explicitly.

---

# Common Review Findings

- EAGER associations
- Entities serialized in API responses
- Unbounded `findAll()`
- N+1 in list endpoints
- Missing `@Version` on concurrently updated entities
- `save()` calls on already-managed entities (harmless but signals confusion)
- Business logic inside entity lifecycle callbacks
- Missing indexes for query predicates (verify with execution plans)

---

# Testing

- `@DataJpaTest` with Testcontainers for the real database dialect; H2 hides dialect bugs.
- Assert SQL query counts on performance-critical paths.
- Test optimistic locking conflict behavior where used.
