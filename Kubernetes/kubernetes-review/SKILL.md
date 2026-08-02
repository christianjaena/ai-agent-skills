---
name: kubernetes-review
description: Review Kubernetes manifests, Helm charts, and workload configuration for resource management, probes, security context, availability, and production readiness. Use this skill when reviewing Deployments, StatefulSets, Helm charts, Kustomize overlays, or preparing workloads for production.
---

# kubernetes-review

Review Kubernetes manifests, Helm charts, and workload configuration for resource management, probes, security context, availability, and production readiness.

## Goal

Catch the manifest mistakes that cause production pain: OOMKills, cascading restarts, dropped traffic during deploys, and privilege escalation.

---

# When to Use

Use this skill whenever:

- Reviewing Deployment, StatefulSet, Job, or CronJob manifests
- Reviewing Helm charts or Kustomize overlays
- Doing a production-readiness review for a new service
- Auditing an existing cluster's workloads

---

# Resources

- Every container sets memory requests AND limits. Memory limit = memory request (avoid overcommit-driven OOMKills of innocent pods).
- CPU: set requests; limits are debatable (throttling risk) — be deliberate and consistent with cluster policy.
- Requests reflect real usage (from metrics), not guesses; wrong requests break scheduling and autoscaling.
- JVM/container-aware runtimes: ensure the runtime respects cgroup limits (modern JVMs do; check `-XX:MaxRAMPercentage`).

---

# Probes

- `readinessProbe`: required for anything behind a Service — gates traffic. Should check "can I serve?" including critical dependencies being reachable at startup.
- `livenessProbe`: only detects unrecoverable states (deadlock). NEVER check dependencies in liveness — a database blip must not restart your whole fleet.
- `startupProbe`: for slow-starting apps (JVMs), instead of huge `initialDelaySeconds`.
- Probe endpoints must be cheap and not require auth.

---

# Availability

- `replicas: 1` means downtime on every deploy and node drain. Minimum 2 for anything production.
- `PodDisruptionBudget` for every multi-replica workload (protects against voluntary disruptions/node upgrades).
- Spread: `topologySpreadConstraints` or anti-affinity across nodes/zones.
- Graceful shutdown: handle SIGTERM, drain in-flight requests; `terminationGracePeriodSeconds` matching real drain time; `preStop` sleep hook to allow endpoint propagation.
- HPA for variable load — target metrics that reflect saturation; never HPA on a workload with `replicas` also managed by GitOps (fights).

---

# Security Context

Baseline for every workload:

```yaml
securityContext:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: true
  capabilities:
    drop: ["ALL"]
  seccompProfile:
    type: RuntimeDefault
```

Also:

- No `privileged: true`, no `hostNetwork`/`hostPID`/`hostPath` without exceptional justification
- Dedicated ServiceAccount per workload, `automountServiceAccountToken: false` unless the API is used
- Secrets via env from Secret refs or mounted files — never in ConfigMaps or image layers
- Image tags pinned (digest or immutable version), never `:latest`
- Non-root images; scan images in CI

---

# Configuration Hygiene

- ConfigMaps/Secrets mounted or env-injected; changes trigger rollout (checksum annotations in Helm).
- Labels: consistent `app.kubernetes.io/*` set; selectors immutable — get them right first time.
- One process per container; sidecars only with purpose.
- Namespaces per team/environment with ResourceQuotas and LimitRanges.
- No naked Pods; everything owned by a controller.

---

# Helm/Kustomize Specifics

- Values have sane defaults; required values fail loudly (`required` function).
- No logic explosions in templates; keep charts readable.
- `helm template | kubectl apply --dry-run=server` or kubeconform in CI.
- Chart versioning follows semver; appVersion tracks the image.

---

# Common Review Findings

- Missing resource requests/limits
- Liveness probe checking database connectivity
- `replicas: 1` with no PDB
- `:latest` image tags
- Containers running as root
- Secrets in ConfigMaps or plain env values
- Missing graceful shutdown handling
- Selector/label mismatches
- CronJobs without `concurrencyPolicy` and deadline

---

# Review Output Format

## Summary

Overall production readiness.

## Issues

For each: severity (Critical / High / Medium / Low / Suggestion), the misconfiguration, failure scenario it causes, corrected snippet.

## Final Recommendation

Ready, ready with fixes, or not production-ready.
