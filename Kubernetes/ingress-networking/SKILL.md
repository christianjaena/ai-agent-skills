---
name: ingress-networking
description: Configure and debug Kubernetes networking including Services, Ingress, Gateway API, TLS termination, DNS, and NetworkPolicies. Use this skill when exposing services, configuring ingress controllers and TLS, restricting traffic with network policies, or debugging connectivity into and inside the cluster.
---

# ingress-networking

Configure and debug Kubernetes networking including Services, Ingress, Gateway API, TLS termination, DNS, and NetworkPolicies.

## Goal

Route traffic into and inside the cluster correctly and securely: right Service types, working TLS, DNS that resolves, and policies that restrict traffic without breaking it.

---

# When to Use

Use this skill whenever:

- Exposing a workload inside or outside the cluster
- Configuring Ingress resources, ingress controllers, or Gateway API
- Setting up TLS termination and cert management
- Writing NetworkPolicies
- Debugging "can't reach the service" problems

---

# Services

| Type | Use |
|---|---|
| ClusterIP | Default; internal service-to-service |
| NodePort | Almost never directly; building block for LBs |
| LoadBalancer | One external L4 entry point per service (costly per-service; prefer one ingress) |
| Headless (`clusterIP: None`) | StatefulSets, client-side discovery, direct pod DNS |
| ExternalName | DNS alias to an external host |

Fundamentals:

- Traffic flows: Service selector → matching READY pods. Empty endpoints = selector mismatch or failing readiness probes; check `kubectl get endpoints` first.
- `port` (service) vs `targetPort` (container) vs `containerPort` (informational) — misalignment is a classic failure.
- Session affinity only via `sessionAffinity: ClientIP` — prefer stateless apps.
- `externalTrafficPolicy: Local` preserves client IPs on LB/NodePort at the cost of potential imbalance.

---

# Ingress

- One ingress controller (NGINX, Traefik, cloud-native like ALB) terminating HTTP(S) for many services beats per-service LoadBalancers.
- Always set `ingressClassName` — clusters often run multiple controllers.
- Path types: `Prefix` vs `Exact` semantics matter; `ImplementationSpecific` varies by controller.
- Controller-specific behavior (rewrites, timeouts, body size, sticky sessions) lives in annotations — they are NOT portable between controllers.
- Backend must be a ClusterIP Service with ready endpoints; ingress 502/503 usually means unhealthy backends, not ingress config.

## Gateway API

The successor to Ingress: `Gateway` + `HTTPRoute` with typed, portable config, cross-namespace routing, and header/traffic-split features without annotation soup. Prefer it for new platform builds; Ingress remains fine for existing simple setups.

---

# TLS

- Terminate TLS at the ingress/gateway; use cert-manager with ACME (Let's Encrypt) or your CA for automated issuance and renewal.
- Cert in a `kubernetes.io/tls` Secret in the SAME namespace as the Ingress.
- Common failures: hostname not matching cert SAN, secret missing/wrong namespace, cert expired because cert-manager challenge failed silently — alert on `Certificate` readiness.
- Internal mTLS at scale: that's a service mesh decision (Istio, Linkerd) — don't hand-roll.

---

# DNS

- Names: `<svc>.<namespace>.svc.cluster.local`; same-namespace callers can use `<svc>`.
- Pods get DNS via CoreDNS; failures cluster-wide usually mean CoreDNS pods are unhealthy or ndots/search-path issues cause slow lookups.
- Debug: `kubectl exec ... -- nslookup <svc>.<ns>`; check CoreDNS logs and metrics.
- External DNS records for ingresses: automate with external-dns, don't hand-manage.

---

# NetworkPolicies

- Policies are additive allowlists; no policy = all traffic allowed (with a policy-enforcing CNI).
- Start with default-deny per namespace, then allow explicitly:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
spec:
  podSelector: {}
  policyTypes: ["Ingress", "Egress"]
```

- Remember to allow: DNS egress to CoreDNS (UDP/TCP 53), ingress from the ingress controller namespace, monitoring scrapers.
- Policies select PODS (labels), not services; both directions (ingress on receiver, egress on sender) must permit the flow when both are restricted.
- Test policies: they fail silently as connection timeouts, the most confusing failure mode in the cluster.

---

# Debugging Connectivity

Work through the chain in order:

1. `kubectl get endpoints <svc>` — empty? Fix selectors/readiness first.
2. curl the pod IP directly from another pod — app listening on the right port/interface (0.0.0.0, not localhost)?
3. curl the service ClusterIP/name — DNS and kube-proxy layer.
4. Check NetworkPolicies in both namespaces.
5. Ingress: controller logs + `describe ingress` backend health + TLS secret.
6. External: LB target health, security groups/firewalls, DNS records.

---

# Common Findings

- App binding to 127.0.0.1 inside the container (unreachable from anywhere)
- Port/targetPort mismatch
- Missing `ingressClassName`
- TLS secret in the wrong namespace
- Default-deny without a DNS egress allowance (everything breaks mysteriously)
- Per-service LoadBalancers where one ingress would do
- NetworkPolicies assumed active on a CNI that doesn't enforce them
