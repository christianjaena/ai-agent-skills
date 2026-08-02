---
name: deployment-strategies
description: Choose and implement Kubernetes deployment strategies including rolling updates, blue-green, canary releases, and rollback procedures with zero-downtime guarantees. Use this skill when configuring rollout behavior, designing release processes, implementing canary or blue-green deployments, or fixing downtime during deploys.
---

# deployment-strategies

Choose and implement Kubernetes deployment strategies including rolling updates, blue-green, canary releases, and rollback procedures with zero-downtime guarantees.

## Goal

Ship every release without dropping traffic, detect bad releases before all users see them, and roll back in seconds — not minutes of panic.

---

# When to Use

Use this skill whenever:

- Configuring Deployment rollout parameters
- Users report errors during deploys (the classic zero-downtime failure)
- Designing canary or blue-green release processes
- Setting up progressive delivery (Argo Rollouts, Flagger)
- Defining rollback procedures

---

# Strategy Selection

| Strategy | Use when | Cost |
|---|---|---|
| Rolling update | Default; stateless services with backward-compatible changes | None extra |
| Blue-green | Instant cutover/rollback needed; big-bang changes; easy full-environment testing | 2x capacity during deploy |
| Canary | High-traffic services where bad releases must be caught on small % of users | Tooling + metrics maturity |
| Recreate | Singletons that cannot run two versions concurrently (rare; question the design) | Downtime |

Whatever the strategy: N and N+1 WILL run concurrently (except Recreate). Every change — API, message schema, database — must be compatible across one version skew. This is non-negotiable and catches more teams than the rollout mechanics.

---

# Zero-Downtime Rolling Updates

The default strategy drops traffic unless ALL of these are in place:

```yaml
strategy:
  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 0     # never go below capacity
```

1. `readinessProbe` that honestly reports "ready to serve" — new pods must not receive traffic early.
2. Graceful shutdown: app handles SIGTERM by finishing in-flight requests, then exits.
3. `preStop` sleep (5–15s): endpoint removal propagates to kube-proxy/ingress AFTER SIGTERM is sent; the sleep covers the race.

```yaml
lifecycle:
  preStop:
    exec:
      command: ["sleep", "10"]
```

4. `terminationGracePeriodSeconds` > preStop + max request duration.
5. `PodDisruptionBudget` so node drains don't take out capacity mid-rollout.

Missing any one of these = intermittent 502/503s during every deploy.

---

# Canary Releases

Manual canary (two Deployments sharing a Service selector) works but is toil. Prefer progressive delivery tooling:

- Argo Rollouts or Flagger: automated traffic shifting with metric analysis
- Steps: 5% → 25% → 50% → 100%, gated on error rate and latency vs baseline
- Automatic rollback when analysis fails

Requirements for canary to be meaningful:

- Reliable success-rate and latency metrics per version (label metrics with version)
- Enough traffic for statistical signal at small percentages
- Session affinity considerations if user experience differs between versions

---

# Blue-Green

- Two full environments; switch Service selector (or ingress/load balancer target) atomically.
- Test green with real smoke tests through the production path before cutover.
- Keep blue warm until confidence window passes; rollback = flip the selector back.
- Database is the hard part: schema must work for both colors — expand/contract migrations, never breaking changes in one step.

---

# Rollbacks

- `kubectl rollout undo` for rolling; selector flip for blue-green; abort for canary tooling.
- Rollback must be rehearsed and take < 1 minute of decision-to-done.
- GitOps: rollback = revert commit; ensure the pipeline is fast enough for emergencies or have a break-glass path.
- Data migrations make rollbacks lie: an N-1 app must work against the N schema (expand/contract discipline).
- Feature flags decouple deploy from release — the cheapest "rollback" is flipping a flag, no deploy at all.

---

# Database Migration Coordination

Expand/contract, always:

1. Expand: add new columns/tables, nullable or defaulted; deploy code that writes both/reads old.
2. Migrate data; deploy code that reads new.
3. Contract: remove old columns only after no running version uses them.

Never combine a destructive migration with the code deploy that depends on it.

---

# Common Findings

- `maxUnavailable: 1` with `replicas: 2` (50% capacity loss during deploy)
- No preStop hook → 502s on every rollout
- Readiness probe returning 200 before the app can actually serve
- Breaking API/schema changes deployed in one step
- Canary without per-version metrics (blind canary)
- Rollback procedure that has never been tested
