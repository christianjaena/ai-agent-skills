---
name: spring-security
description: Implement and review Spring Security configuration including authentication, authorization, JWT/OAuth2, CSRF, CORS, and method security. Use this skill when working with Spring Security filter chains, securing endpoints, configuring OAuth2/JWT resource servers, or reviewing authentication and authorization code.
---

# spring-security

Implement and review Spring Security configuration including authentication, authorization, JWT/OAuth2, CSRF, CORS, and method security.

## Goal

Secure Spring applications correctly: deny by default, authorize explicitly, and avoid the common misconfigurations that silently disable protection.

---

# When to Use

Use this skill whenever:

- Configuring a `SecurityFilterChain`
- Adding authentication (form, JWT, OAuth2, API keys)
- Adding endpoint or method-level authorization
- Reviewing security configuration changes
- Debugging 401/403 issues or CORS failures

---

# Core Principles

- Deny by default: end authorization rules with `.anyRequest().authenticated()` (or `denyAll`), never `permitAll`.
- Authorization rule order matters: most specific matchers first.
- Authentication answers "who are you"; authorization answers "what may you do". Keep them distinct.
- Never build your own crypto, session tokens, or password hashing. Use the framework.

---

# Filter Chain Configuration

- Use the lambda DSL with `SecurityFilterChain` beans (the `WebSecurityConfigurerAdapter` is long gone).
- Multiple chains: order with `@Order`, scope with `securityMatcher`.
- Stateless APIs: `SessionCreationPolicy.STATELESS` and disable form login.
- Only disable CSRF for genuinely stateless token-based APIs; keep it for cookie/session-based apps.

---

# Authentication

- Passwords: `BCryptPasswordEncoder` or `DelegatingPasswordEncoder`. Never MD5/SHA-x, never plaintext.
- JWT resource server: use `spring-boot-starter-oauth2-resource-server`; validate issuer, audience, expiry, and signature. Do not hand-roll JWT parsing filters.
- Keep access tokens short-lived; use refresh tokens for longevity.
- Never log tokens, credentials, or Authorization headers.

---

# Authorization

- URL rules for coarse-grained access; method security for business-level rules.
- Enable with `@EnableMethodSecurity` and use `@PreAuthorize`.
- Distinguish roles (`hasRole("ADMIN")`) from authorities/scopes (`hasAuthority("SCOPE_read")`).
- Enforce object-level authorization: verify the authenticated user owns the resource being accessed. URL checks alone don't stop IDOR.
- Never trust client-supplied IDs, roles, or flags for authorization decisions.

---

# CORS

- Configure CORS via `CorsConfigurationSource` integrated with the security chain, not a random `WebMvcConfigurer` alone.
- Never combine `allowedOrigins("*")` with `allowCredentials(true)`.
- Allowlist exact origins in production.

---

# Common Vulnerabilities to Catch

- `permitAll()` on overly broad matchers (e.g. `/**` orderings that shadow protected routes)
- CSRF disabled on session-based apps
- Missing object-level ownership checks (IDOR)
- Role checks in controllers duplicated inconsistently instead of centralized
- Secrets and signing keys committed to the repository
- Actuator endpoints exposed without authentication
- User enumeration via different error messages for unknown user vs wrong password
- Missing `@EnableMethodSecurity` so `@PreAuthorize` silently does nothing

---

# Testing Security

- `@WebMvcTest` with `@WithMockUser` / `@WithAnonymousUser` for rule verification.
- Test both allowed and denied cases for each rule.
- Test authorization at the method level, including ownership checks.
- Integration-test the JWT path with a locally signed token or mock JWT decoder.

---

# Review Output Format

## Summary

Overall security posture of the change.

## Findings

For each: severity (Critical / High / Medium / Low), the vulnerability or misconfiguration, exploit scenario, recommended fix.

## Final Recommendation

Safe to merge, merge with fixes, or block until resolved.
