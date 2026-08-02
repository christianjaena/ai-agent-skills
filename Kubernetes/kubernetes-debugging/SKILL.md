---
name: kubernetes-debugging
description: Diagnose Kubernetes problems systematically including pod crashes, CrashLoopBackOff, OOMKilled, pending pods, service connectivity, DNS, and node issues. Use this skill when pods won't start, workloads crash or restart, traffic doesn't reach services, or investigating any Kubernetes cluster misbehavior.
---

# kubernetes-debugging

Diagnose Kubernetes problems systematically including pod crashes, CrashLoopBackOff, OOMKilled, pending pods, service connectivity, DNS, and node issues.

## Goal

Work from symptom to root cause using the cluster's own evidence — events, statuses, logs — instead of restarting things and hoping.

---

# When to Use

Use this skill whenever:

- Pods are Pending, CrashLoopBackOff, ImagePullBackOff, OOMKilled, or Evicted
- A Service or Ingress isn't receiving/routing traffic
- Deployments won't progress or rollouts hang
- Nodes are NotReady or workloads get evicted
- Anything in a cluster behaves unexpectedly

---

# First Commands, Always

```bash
kubectl get pods -o wide            # status, restarts, node placement
kubectl describe pod <pod>          # EVENTS at the bottom answer most questions
kubectl logs <pod> --previous       # logs from the crashed container, not the new one
kubectl get events --sort-by=.lastTimestamp
```

`describe` events + `logs --previous` solve the majority of pod issues. Read them before forming theories.

---

# Pod Won't Start

## Pending

Scheduler can't place it. Check `describe` events:

- `Insufficient cpu/memory`: requests too large or cluster full — check `kubectl describe nodes` allocations
- Unsatisfiable nodeSelector/affinity/taints: compare pod spec to node labels/taints
- Volume issues: PVC unbound (`kubectl get pvc`), wrong storage class, zone mismatch

## ImagePullBackOff

- Typo'd image or tag; tag doesn't exist
- Private registry: missing/wrong `imagePullSecrets`
- Rate limits (Docker Hub); check event message for the exact registry error

## CreateContainerConfigError

- Referenced ConfigMap/Secret key doesn't exist — event names the missing key

---

# Pod Crashes

## CrashLoopBackOff

The container starts and exits. Find out why it exits:

```bash
kubectl logs <pod> --previous
kubectl describe pod <pod>   # check Last State: exit code
```

- Exit code 1/other app codes: application error — read its logs
- Exit code 137: OOMKilled or SIGKILL (see below)
- Exits immediately with no logs: bad command/entrypoint, missing config, failed migrations
- Passes initially then killed: failing liveness probe — check probe config before blaming the app

## OOMKilled (exit 137, reason OOMKilled)

- Raise the memory limit only after understanding usage: `kubectl top pod`, memory profile the app
- JVM: heap + metaspace + threads + native must fit the LIMIT; set `-XX:MaxRAMPercentage` sensibly
- Check for leaks: is usage a plateau or a ramp?

## Evicted

- Node pressure (memory/disk). Check `kubectl describe node` conditions; fix requests/limits or node sizing.

---

# Service / Networking Issues

Work down the chain:

1. Endpoints exist? `kubectl get endpoints <svc>` — empty means selector doesn't match pod labels, or pods aren't Ready (readiness probe failing).
2. Service port vs containerPort vs targetPort aligned?
3. Direct pod reachable? `kubectl exec` into another pod, `curl <pod-ip>:<port>`.
4. DNS: `kubectl exec ... -- nslookup <svc>.<ns>.svc.cluster.local`; check CoreDNS pods/logs if broken.
5. NetworkPolicy blocking? Check policies in both namespaces (default-deny?).
6. Ingress: controller logs, backend health in `kubectl describe ingress`, TLS secret validity.

Most "service down" issues are readiness probes failing → empty endpoints.

---

# Rollout Stuck

```bash
kubectl rollout status deploy/<name>
kubectl describe deploy/<name>
kubectl get rs   # is the new ReplicaSet scaling?
```

- New pods failing (see pod sections above) while `maxUnavailable: 0` blocks progress
- PDB preventing old pod termination
- `progressDeadlineSeconds` exceeded — the deploy condition tells you

Rollback while investigating: `kubectl rollout undo deploy/<name>`.

---

# Node Issues

- `kubectl get nodes`, `kubectl describe node <node>`: conditions (MemoryPressure, DiskPressure, NotReady), taints, allocations
- kubelet/containerd logs on the node for NotReady causes
- Cordon + drain for suspect nodes; don't debug workloads on a sick node

---

# Ephemeral Debugging Tools

```bash
kubectl debug -it <pod> --image=busybox --target=<container>   # attach debug container
kubectl debug node/<node> -it --image=busybox                   # node inspection
kubectl exec -it <pod> -- sh
kubectl port-forward svc/<svc> 8080:80
```

Distroless/minimal images: `kubectl debug` is the way in.

---

# Anti-Patterns

- Deleting pods repeatedly to "fix" CrashLoopBackOff without reading `--previous` logs
- Raising memory limits blindly on every OOMKill
- Debugging with `latest` images so you can't tell what's actually running
- Ignoring events because logs "should" have the answer

---

# Output Format

## Symptom

Observed state and scope.

## Evidence

Key events, statuses, log lines gathered.

## Root Cause

The mechanism, tied to evidence.

## Fix and Prevention

Change applied; alert/probe/resource correction to prevent recurrence.
