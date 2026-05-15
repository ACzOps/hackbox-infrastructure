# ADR-005: Secret storage for the platform

## Status
Accepted (2026-04-28)

## Context
We need secure secret storage that can replace or extend the built-in Kubernetes Secret model on k3s. Because we standardize on Kubernetes, we prefer Kubernetes-native or Kubernetes-friendly integrations (operators, sidecars, or clear API contracts) rather than ad-hoc host-only vaults.

Requirements include practical day-two operations for a small team, a path to rotation and access control, and a realistic operational footprint on our hardware.

## Options considered

### Option A: OpenBao

+ Open source fork lineage familiar to teams that used HashiCorp Vault patterns
+ Designed with HA and policy primitives in mind
- Higher setup and maintenance burden for our current team size
- Smaller ecosystem and fewer “batteries included” integrations than some SaaS-first alternatives

### Option B: Infisical

+ Fast setup, good operator experience, and a strong GUI for onboarding
+ Fits Kubernetes-oriented secret injection workflows we care about
- Depends on Redis and PostgreSQL when run in-cluster, which adds moving parts we must operate

### Option C: Akeyless

+ Managed-style feature set for enterprises that want SaaS boundaries
- Closed source and limited community-visible operational detail for our self-hosted posture

## Decision

We chose **Infisical** as the primary secret platform.

We accept the extra operational cost of running Redis and PostgreSQL alongside Infisical because the usability and integration story outweighs that burden for now, and it refreshes practical DevOps experience with stateful dependencies we already need to reason about elsewhere.

## Consequences

- Positive: clearer workflows for developers and operators than “secrets only in etcd”
- Positive: GUI and APIs reduce friction for common rotation and access patterns
- Negative: additional databases and cache to backup, upgrade, and monitor
- Negative: dependency on vendor roadmap for free/community features requires periodic reassessment

## References

- [Infisical documentation](https://infisical.com/docs)
- [OpenBao documentation](https://openbao.org/docs/)
- ADR-002: Infrastructure shape for service orchestration
