---
name: java-modernization
description: Modernize Java codebases to current language features and idioms including records, sealed types, pattern matching, switch expressions, text blocks, Optional, Streams, and migration from legacy Java versions. Use this skill when upgrading Java versions, refactoring legacy Java code, or reviewing code for modern idiom adoption.
---

# java-modernization

Modernize Java codebases to current language features and idioms including records, sealed types, pattern matching, switch expressions, text blocks, Optional, Streams, and migration from legacy Java versions.

## Goal

Move Java code to modern, expressive idioms where they genuinely improve clarity and safety — not modernization for its own sake.

---

# When to Use

Use this skill whenever:

- Upgrading a project's Java version (e.g. 8/11 → 17/21+)
- Refactoring legacy Java code
- Reviewing code that could use modern idioms
- Deciding whether a new language feature fits a use case

---

# Modernization Priorities

Apply in order of payoff:

1. Records for data carriers — kills boilerplate and mutability bugs
2. Pattern matching (`instanceof`, `switch`) — removes cast noise and missed-case bugs
3. Sealed interfaces + exhaustive `switch` — compiler-checked domain modeling
4. Switch expressions — no fall-through bugs, values not statements
5. Text blocks for SQL/JSON/HTML literals
6. `var` for obvious local types
7. Immutable collections (`List.of`, `Collectors.toUnmodifiableList`)

---

# Records

Use for: DTOs, value objects, API request/response types, map keys, compound return values.

```java
public record Money(BigDecimal amount, Currency currency) {
    public Money {
        if (amount.scale() > currency.getDefaultFractionDigits())
            throw new IllegalArgumentException("scale exceeds currency");
    }
}
```

- Compact constructors for validation.
- Don't use records for JPA entities (proxies and mutability requirements conflict).
- A record with 8+ components or business behavior sprawl wants to be a class.

---

# Sealed Types + Pattern Matching

Model closed domains so the compiler enforces completeness:

```java
public sealed interface PaymentResult permits Approved, Declined, Pending {}

// exhaustive - compiler errors when a new case is added
return switch (result) {
    case Approved a -> settle(a.transactionId());
    case Declined d -> notifyFailure(d.reason());
    case Pending p -> schedule(p.retryAt());
};
```

- No `default` branch in switches over sealed types — losing exhaustiveness checking defeats the purpose.
- Record patterns destructure inline: `case Approved(var txId, var amount) -> ...`

---

# Optional Discipline

- Return type for "may be absent" — that's it.
- Never: fields, parameters, collections of Optionals, `Optional.get()` without check.
- Prefer `map`/`filter`/`orElseGet` chains over `isPresent()` + `get()`.
- Don't wrap collections in Optional; return empty collections.

---

# Streams Judgment

- Streams for transformation pipelines; loops for side effects, early exit with state, or index math.
- If a stream needs comments to explain, write the loop.
- `.parallel()` only with measurement, sizable data, and no shared mutable state.
- `Collectors.groupingBy`/`toMap` replace most manual map-building loops (mind `toMap` duplicate-key throws).

---

# Version Migration Checklist

Upgrading past 11 → 17 → 21:

- Removed/encapsulated internals: `sun.misc.*`, deep reflection into JDK — check with `jdeps`; add `--add-opens` only as a bridge, fix properly
- SecurityManager, Applets, CMS GC, Nashorn: gone — replace usages
- Dependency floor: Spring Boot 3.x requires 17 and `jakarta.*` (the big rename from `javax.*`)
- Build tooling: modern Maven/Gradle versions and compiler plugin config
- Run the full suite on the new version BEFORE adopting new syntax; separate "runs on N" from "uses N features" into two steps

---

# Anti-Patterns

- Rewriting readable loops into unreadable stream chains to look modern
- `var` where the type isn't obvious from the right-hand side
- Records made mutable via setter-like wither sprawl instead of using a class
- Modernizing and changing behavior in the same commit — modernization refactors must be behavior-preserving and test-covered

---

# Output Format for Modernization Reviews

## Opportunities

Ranked list: current pattern → modern replacement, with the concrete benefit (safety, brevity, exhaustiveness).

## Risks

Behavior changes to guard against; test coverage gaps.

## Migration Order

Stepwise plan, each step independently shippable.
