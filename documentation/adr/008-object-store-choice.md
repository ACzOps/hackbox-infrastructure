# ADR-008: Object storage for workloads on k3s

## Status
Accepted (2026-05-03)

## Context
Some applications we plan to run on k3s need S3-compatible object storage in addition to block volumes. Examples include Terraform remote state and other blob-oriented artifacts.

We want a solution we can self-host on constrained hardware, with clear licensing, and without taking on unnecessary distributed-storage complexity for a first iteration.

## Options considered

### Option A: Garage

+ Lightweight and open source, designed for small clusters and homelab-style topologies
+ Works well with common S3 clients and the AWS CLI compatibility surface we already use
+ Reasonable operational surface for a small team
- Younger ecosystem than the largest incumbents; fewer third-party “enterprise support” options

### Option B: MinIO

+ Industry standard name in self-hosted object storage conversations
+ Broad client compatibility
- Licensing and product direction moved away from a simple “fully open community edition” story we are comfortable betting on long term
- For our principles, current licensing posture disqualifies it as the default choice

### Option C: Ceph

+ Production-grade distributed storage when operated at scale
+ Rich feature set beyond object (block and file) if we ever converge storage tiers
- High infrastructure and operational cost relative to our current node count and RAM budget
- Complexity is not justified until object storage becomes a central platform primitive rather than a targeted capability

#### Other options

We briefly looked at newer projects such as RustFS and SeaweedFS, but skipped them for now because small teams and fast-moving APIs increase upgrade and compatibility risk for foundational storage.

## Decision

We chose **Garage** as the S3-compatible object store for this environment.

It is the best trade-off between resource use, open licensing, and operational simplicity for our near-term needs, while we explicitly defer Ceph-class platforms until requirements justify that complexity.

## Consequences

- Positive: a single self-hosted object endpoint for Terraform state and similar use cases without a Ceph-sized footprint
- Positive: stays aligned with open tooling and predictable client behavior
- Negative: we must operate replication, TLS, and backup discipline ourselves (no managed cloud boundary)

## References

- [Garage documentation](https://garagehq.deuxfleurs.fr/documentation/quick-start/)
- [Ceph documentation](https://docs.ceph.com/)
- ADR-002: Infrastructure shape for service orchestration
