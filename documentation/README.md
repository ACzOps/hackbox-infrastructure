# Hackbox.cc infrastructure documentation

The homelab described in this repository is **experimental**: tinkering with the stack, validating tools and workflows before anything might be promoted to a “real” environment. This documentation exists to make orientation easier and to preserve context between work sessions.

## Where things live

**Physical architecture** (hardware, hosts, network at a glance): [physical-architecture.md](physical-architecture.md).

**Service architecture** (overview of services running in the homelab): [service-architecture.md](service-architecture.md).

**Architecture Decision Records (ADRs)** capture *what* we chose, *why*, and the consequences, so we do not have to rediscover the reasoning from scratch later. Individual records live under [`adr/`](adr/).

## Automation layout

Initial **bare-metal host provisioning** is driven by **Ansible playbooks** in [`../ansible/playbooks/`](../ansible/playbooks/).

**Kubernetes-oriented infrastructure** (manifests, Helm values, and related GitOps sources) lives under [`../k3s/`](../k3s/) and is maintained in a **GitOps** model: desired state is declared in Git and reconciled into the cluster (see [ADR-004](adr/004-k3s-cd-tooling.md) for the CD controller choice).
