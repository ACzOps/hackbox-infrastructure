# ADR-007: Primary relational database on Kubernetes

## Status
Accepted (2026-04-29)

## Context
We need a solid, industry-standard relational database that fits the Kubernetes ecosystem for stateful workloads (application data, supporting services for platform components, and similar).

The choice should favor operational clarity on a small cluster, strong consistency for transactional workloads, and a path to automated backup/restore and upgrades without bespoke snowflake automation.

## Options considered

### Option A: CloudNativePG (CNPG) — PostgreSQL operator

+ PostgreSQL matches our bias toward standards-rich SQL, MVCC concurrency, and extension ecosystem
+ CNPG is purpose-built for Kubernetes lifecycle (failover, backup hooks, upgrades) rather than “PostgreSQL on a VM, copied into a Pod”
+ Aligns with common Helm/chart patterns and GitOps-friendly CustomResources
- Requires learning operator-specific resources and failure modes
- Stateful workloads still demand careful storage class and backup verification

### Option B: MySQL (or MySQL-compatible operators)

+ Familiar to many developers; wide hosting compatibility outside Kubernetes too
+ Some ecosystems standardize on MySQL dialects or replication topologies
- Feature and consistency trade-offs versus PostgreSQL for complex queries and advanced SQL
- For our platform direction, PostgreSQL-first tooling is a better default than maintaining parallel RDBMS expertise

### Option C: MariaDB

+ Open lineage with good performance for simpler read-heavy patterns
+ Often “good enough” for straightforward schemas at large scale
- For complex transactional workloads, advanced SQL, and extension needs, PostgreSQL is the stronger long-term default
- Adds a second “MySQL-family” operational path without clear benefit given our PostgreSQL preference

## Decision

We chose **CloudNativePG (PostgreSQL)** as the default relational database mechanism on Kubernetes.

CNPG gives us a Kubernetes-native operational model while keeping the database engine on PostgreSQL, which we prefer for correctness, concurrency semantics, and ecosystem fit.

## Consequences

- Positive: one primary RDBMS story (PostgreSQL via CNPG) simplifies backups, monitoring, and upgrades
- Positive: stronger fit for transactional application data than splitting attention across multiple engines
- Negative: teams must understand operator CRDs and PostgreSQL-specific tuning (connections, memory, autovacuum)
- Negative: stateful storage and restore drills are mandatory before trusting production data paths

## References

- [CloudNativePG documentation](https://cloudnative-pg.io/documentation/current/)
- [PostgreSQL documentation](https://www.postgresql.org/docs/)
- ADR-002: Infrastructure shape for service orchestration
