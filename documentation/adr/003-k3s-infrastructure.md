# ADR-003: k3s cluster topology (control plane and worker layout)

## Status

Accepted (2026-04-03)

## Context

We run k3s on a fixed pool of four thin client hosts (see ADR-002). We must decide how many machines act as Kubernetes control plane (k3s server) versus worker (k3s agent) nodes.

Goals for this platform:

- maximize capacity for **tooling workloads** (CI/CD, observability, ingress, and similar services) rather than for redundant control-plane components
- stay within the **physical machine count** we actually have
- keep operational complexity proportionate to team size

The hardware budget does **not** allow a classic “production HA” layout such as **three dedicated control-plane nodes plus two workers** (five machines). We also want to avoid spending most RAM and CPU on etcd and API servers while workers remain undersized, which would undermine the purpose of a **performant tool platform**.

## Options considered

### Option A: One control plane, three worker nodes (1 + 3)

+ Maximizes aggregate CPU and RAM for **application and tool workloads**
+ Simple mental model: a single k3s server and three agents
+ Matches the available fleet size without leaving machines idle in non-workload roles
- **Single point of failure (SPOF)** for the control plane: a serious failure or maintenance on the server node affects cluster management and scheduling until recovery

### Option B: Two control planes, two workers (2 + 2)

+ Looks closer to “HA” on paper because there are two server nodes
- etcd / quorum: a **two-node** control plane is an awkward fit for highly available Kubernetes control planes; you still lack a clean odd-member quorum, and failure modes are harder to reason about than with one or three servers
- **Split-brain risk:** if the network partitions, two subsets of nodes may each believe they should lead or accept writes, so the cluster can disagree about **who is authoritative** for cluster state (API objects, etcd leadership). Without proper quorum design, that can cause **inconsistent or divergent state** instead of a clean “one side wins” behavior
- Splits scarce RAM across two control-plane nodes, leaving **less** headroom per worker than Option A for the same total hardware

### Option C: Three control planes, one worker (3 + 1)

+ Stronger control-plane redundancy for etcd and API availability
- **Strong overkill** for our scale: three machines would be dedicated mostly to control-plane overhead while **only one** node runs general workloads, which is the opposite of our priority (a capable tooling platform)
- Poor utilization of the fleet for actual services

## Decision

We adopt **Option A: one k3s server (control plane) and three k3s agents (workers)** — **1 + 3**.

We explicitly accept the **control-plane SPOF** as a trade-off: incident or maintenance on the server node is a major operational event, and recovery planning (backups, restore procedures, and monitoring) must reflect that.

## Consequences

- Positive: **most cluster resources** are available for workloads that deliver value (tools, pipelines, ingress, observability stacks)
- Positive: **simpler** cluster topology and fewer moving parts for a small team
- Negative: **no control-plane HA**; design monitoring, backups, and runbooks around server-node failure and restore
- Negative: planned maintenance on the control-plane host requires a **coordination window** (or brief downtime) unless cold standby/restore procedures are used

## References

- [k3s documentation — High Availability](https://docs.k3s.io/installation/ha)
- ADR-002: Infrastructure shape for service orchestration
