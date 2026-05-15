# ADR-004: Continuous delivery tooling for the k3s cluster

## Status
Accepted (2026-04-18)

## Context
We need software to continuously provision and reconcile Kubernetes tooling on our k3s cluster. We experimented with Ansible-driven installs, but the amount of YAML and operational drift became hard to manage as the footprint grew.

The choice should fit a small team, align with GitOps-style workflows where possible, and support infrastructure-style workloads (ingress, observability, CI helpers, and similar) rather than only ad-hoc application deploys.

## Options considered

### Option A: Argo CD

+ Widely adopted GitOps controller with a strong UI-first experience
+ Mature patterns for application promotion and multi-environment setups
+ Large community and example material
- Slightly different defaults and extension points than Flux for cluster-scoped bootstrap

### Option B: Flux CD

+ Strong fit for infrastructure provisioning and cluster bootstrap (sources, kustomize, helm controllers)
+ Lightweight operator model aligned with “reconcile from Git” without a large UI dependency
+ Good match when the primary goal is declarative cluster state rather than a single product deployment pipeline
- Steeper learning curve for contributors used only to Argo CD’s UI workflows
- Operational visibility relies more on Kubernetes-native tooling than a built-in console

### Option C: Ansible-only (or similar) push-based automation

+ Familiar imperative/procedural model for one-off changes
+ No additional in-cluster controller beyond what we already run for bootstrap
- Drift between “what Git says” and “what the cluster is” is easy to accumulate
- YAML sprawl and lifecycle management did not scale well in our trial

## Decision

We chose **Flux CD** as the continuous delivery mechanism for Kubernetes on this cluster.

After reviewing material and feedback from DevOps communities, Flux better matches our emphasis on infrastructural provisioning and ongoing reconciliation. Argo CD remains a strong option for teams that prioritize application-centric GitOps with a rich UI; for our current scope, Flux is the better default.

## Consequences

- Positive: a single reconciliation model from Git reduces manual Ansible edits for routine cluster changes
- Positive: aligns day-two operations with Kubernetes-native controllers and Helm/Kustomize sources
- Negative: team must maintain Flux-specific concepts (GitRepository, Kustomization, HelmRelease, and upgrade discipline)
- Negative: contributors who prefer a heavy GitOps UI will rely on external dashboards or kubectl workflows

## References

- [Flux documentation](https://fluxcd.io/flux/)
- [Argo CD documentation](https://argo-cd.readthedocs.io/)
- ADR-002: Infrastructure shape for service orchestration
- ADR-003: K3s cluster topology (control plane and worker layout)
