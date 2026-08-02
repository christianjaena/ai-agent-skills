---
name: spring-boot-review
description: Review Spring Boot application code for correct layering, dependency injection, configuration, validation, error handling, and idiomatic framework usage. Use this skill when reviewing or writing Spring Boot controllers, services, repositories, configuration classes, or application setup.
---

# spring-boot-review

Review Spring Boot application code for correct layering, dependency injection, configuration, validation, error handling, and idiomatic framework usage.

## Goal

Ensure Spring Boot code follows framework idioms and clean layering so applications stay testable, maintainable, and predictable in production.

---

# When to Use

Use this skill whenever:

- Reviewing Spring Boot pull requests
- Writing new controllers, services, or repositories
- Reviewing configuration and application setup
- Auditing an existing Spring Boot codebase
- Reviewing AI-generated Spring Boot code

---

# Layering

- Controllers: HTTP concerns only — request mapping, validation triggers, response shaping. No business logic.
- Services: business logic and transaction boundaries.
- Repositories: persistence only. No business rules.
- Avoid calling repositories directly from controllers.
- DTOs at the API boundary; never expose JPA entities in responses.

---

# Dependency Injection

- Constructor injection only. No field injection with `@Autowired`.
- Mark dependencies `final`.
- Avoid `@Autowired` on constructors (implicit since Spring 4.3 with a single constructor).
- Avoid circular dependencies; they signal a design problem, not a need for `@Lazy`.
- Prefer explicit `@Configuration` beans over component scanning for third-party classes.

---

# Configuration

- Externalize configuration; never hardcode environment-specific values.
- Use `@ConfigurationProperties` with a typed, validated record/class instead of scattered `@Value`.
- Validate configuration at startup (`@Validated` on properties classes).
- Keep profiles for environments, not for business behavior.
- Secrets come from a secret manager or environment, never from committed files.

---

# Validation and Error Handling

- Validate request bodies with Bean Validation (`@Valid`, constraint annotations).
- Centralize error handling in `@RestControllerAdvice`.
- Return consistent error response shapes (prefer RFC 7807 problem details).
- Map exceptions to correct HTTP status codes; never leak stack traces to clients.
- Don't catch-and-swallow; either handle meaningfully or let the advice translate it.

---

# Common Review Findings

- Business logic in controllers
- Entities returned from REST endpoints
- Field injection
- `@Transactional` on controller methods
- Missing `@Valid` on request bodies
- Broad `catch (Exception e)` blocks
- Blocking calls inside reactive pipelines
- `RestTemplate` without timeouts (prefer `RestClient`/`WebClient` with explicit timeouts)
- Fat `@SpringBootApplication` class doing work in `main`
- Test slices missing: full `@SpringBootTest` used where `@WebMvcTest`/`@DataJpaTest` suffices

---

# Testing

- Unit test services without Spring context where possible.
- Use slice tests: `@WebMvcTest` for controllers, `@DataJpaTest` for repositories.
- Reserve `@SpringBootTest` for genuine integration tests.
- Use Testcontainers for real database/broker integration tests.
- Don't mock what you own trivially; don't hit real external services in tests.

---

# Observability Basics

- Expose health, metrics, and info via Actuator; secure the endpoints.
- Use structured logging with correlation IDs.
- Add Micrometer metrics for key business operations.

---

# Review Output Format

## Summary

Overall assessment of the change.

## Issues

For each: severity (Critical / High / Medium / Low / Suggestion), why it matters, recommended fix.

## Final Recommendation

Approve, approve with suggestions, or request changes.
