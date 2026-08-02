---
name: aws-iam
description: Design and review AWS IAM policies, roles, and permissions following least privilege, covering policy evaluation, trust policies, resource policies, and common misconfigurations. Use this skill when writing IAM policies, creating roles for services or humans, reviewing permissions, or debugging AccessDenied errors.
---

# aws-iam

Design and review AWS IAM policies, roles, and permissions following least privilege, covering policy evaluation, trust policies, resource policies, and common misconfigurations.

## Goal

Grant exactly the access each principal needs — no wildcards by habit, no long-lived credentials, no accidental public exposure.

---

# When to Use

Use this skill whenever:

- Writing or reviewing IAM policies, roles, or trust relationships
- Creating roles for Lambda, ECS, EC2, or CI/CD pipelines
- Debugging `AccessDenied` errors
- Reviewing bucket policies, KMS key policies, or other resource policies
- Auditing an account's permission posture

---

# Core Principles

- Least privilege: specific actions on specific resources with conditions. Start narrow, widen with evidence (Access Analyzer, CloudTrail).
- Roles over users: humans federate via Identity Center; workloads assume roles. No long-lived access keys.
- Explicit Deny always wins; then evaluation requires an explicit Allow; default is deny.
- Permissions are code: version controlled, reviewed, deployed via IaC — not hand-edited in the console.

---

# Policy Evaluation (what actually decides access)

Order of evaluation for a request:

1. Explicit Deny anywhere → denied.
2. Organizations SCPs must allow.
3. Resource-based policy allow can grant access directly (same-account).
4. Identity-based policy must allow (cross-account: BOTH sides must allow).
5. Permissions boundary, if present, caps what identity policies can grant.
6. Session policies cap assumed-role sessions.

Debugging `AccessDenied`: identify which layer denied — the error message usually names the policy type. Use the IAM Policy Simulator and CloudTrail `errorCode` events.

---

# Writing Good Policies

- Scope `Resource` to ARNs, not `*`, unless the action genuinely doesn't support resource-level permissions (check the service authorization reference).
- Use conditions: `aws:SourceArn`/`aws:SourceAccount` (confused deputy prevention), `aws:PrincipalOrgID`, `aws:SecureTransport`, IP/VPC conditions where meaningful.
- Separate read and write policies; compose.
- `NotAction`/`NotResource` with `Allow` is almost always a mistake — use them only with Deny, carefully.
- Never grant `iam:*`, `iam:PassRole` with `Resource: *`, or `sts:AssumeRole` on `*` — these are privilege escalation paths.

`iam:PassRole` deserves special attention: whoever can pass a powerful role to a service they control effectively has that role's permissions. Always scope PassRole to specific role ARNs and add `iam:PassedToService`.

---

# Trust Policies

- Trust policy = WHO can assume the role. Permission policy = WHAT the role can do. Review both.
- Service roles: condition on `aws:SourceArn`/`aws:SourceAccount` to prevent confused-deputy.
- Cross-account: require `ExternalId` for third parties.
- CI/CD (GitHub Actions etc.): use OIDC federation with `sub` conditions pinned to repo and branch — never store AWS keys in CI secrets.

---

# Common Misconfigurations

- `"Action": "*", "Resource": "*"` in anything but a break-glass role
- S3 bucket policies with `Principal: "*"` (public) unintentionally
- Unscoped `iam:PassRole`
- Access keys for workloads that could use roles
- Roles trusted by entire accounts (`root` principal) when a specific role would do
- KMS key policies granting `kms:*` to the account root plus everyone via IAM
- Permissions copied from tutorials with `AdministratorAccess` "temporarily"
- Missing MFA/conditions on sensitive human operations

---

# Auditing Tools

- IAM Access Analyzer: external access findings and least-privilege policy generation from CloudTrail.
- Credential report: stale users and keys.
- Service last accessed data: prune unused permissions.
- CloudTrail: who did what; source of truth for tightening.

---

# Review Output Format

## Summary

Overall risk posture of the change.

## Findings

For each: severity (Critical / High / Medium / Low), the over-grant or misconfiguration, escalation/exposure scenario, minimal-privilege fix with the corrected policy snippet.

## Final Recommendation

Safe to apply, apply with fixes, or block.
