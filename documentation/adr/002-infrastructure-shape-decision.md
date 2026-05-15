# ADR-002: Infrastructure shape for service orchestration

## Status
Accepted (2026-04-02)

## Context
We need a predictable infrastructure baseline for self-hosted services, including CI/CD, secret management, observability, reverse proxying, and identity tooling.
Our current hardware pool for this platform consists of four Dell Wyse 5070 thin clients, each with:
- 8 GB RAM
- minimum 128 GB SSD
- CPU: Intel Pentium Silver J5005

The selected orchestration approach must:
- run reliably on low-power hardware with constrained RAM
- keep operational complexity at a manageable level for a small team
- support gradual growth of services without redesigning the whole platform
- provide a clear and modern deployment model for containerized workloads

## Options considered

### Option A: K3s on Debian 13

+ Lightweight Kubernetes distribution suitable for edge and homelab hardware
+ Lower control-plane overhead than full upstream Kubernetes
+ Strong ecosystem compatibility (Helm charts, Kubernetes manifests, common tooling)
+ Good long-term portability of operational knowledge to larger Kubernetes environments
+ Debian 13 provides a stable and familiar Linux base for maintenance and security updates
- Still requires Kubernetes operational learning (networking, storage, manifests, upgrades)
- On four nodes with 8 GB RAM each, careful resource limits and retention policies are required

### Option B: Docker Swarm

+ Simpler initial setup and lower entry barrier for container orchestration
+ Fast bootstrap for small clusters
+ Lower cognitive load for teams already familiar with Docker Compose workflows
- Smaller ecosystem and weaker momentum compared to Kubernetes
- Reduced portability of platform patterns and tooling compared to Kubernetes-first environments
- Fewer standardized options for advanced scheduling, policy, and multi-tool integrations

### Option C: Kubernetes on Talos OS

+ Strong security-oriented and immutable operating model
+ Clean Kubernetes-focused lifecycle and API-driven node management
+ Good fit for teams aiming at a strict platform-engineering approach
- Higher operational entry cost for current team and current phase
- More complexity than needed for maintenance-network tooling on the current thin-client hardware
- Full Kubernetes platform will be launched later on virtual machines hosted on larger servers (two Dell PowerEdge R420 machines running on Proxmox VE 9.1), making a separate Talos-based cluster as applications' platform

## Decision

We chose **kKs on Debian 13** for all four Dell Wyse 5070 nodes.
This option provides the best balance between resource efficiency and long-term platform capabilities. It keeps the runtime lightweight enough for the available hardware while aligning operations with the Kubernetes ecosystem used broadly across modern infrastructure.
We explicitly do not build a separate full Kubernetes-on-Talos cluster for maintenance-network tooling at this stage, because the primary full Kubernetes platform is planned on virtual machines running on larger hardware.

## Consequences

- Positive: unified cluster model on all four nodes simplifies operational consistency
- Positive: better long-term extensibility and tooling compatibility than Docker Swarm
- Positive: team knowledge gained on k3s transfers to standard Kubernetes concepts and practices
- Positive: Debian 13 offers predictable system administration, package management, and security maintenance
- Negative: higher initial operational complexity than Docker Swarm
- Negative: resource planning must be strict on 8 GB RAM nodes (requests/limits, retention, and placement constraints)
- ~~Negative~~ Positive: Talos-based operational patterns are postponed and will be revisited when the VM-based Kubernetes platform on larger servers is introduced

## References

- [k3s documentation](https://docs.k3s.io/)
- [Debian documentation](https://www.debian.org/doc/)
- [Docker Swarm documentation](https://docs.docker.com/engine/swarm/)
- [Talos documentation](https://www.talos.dev/)
