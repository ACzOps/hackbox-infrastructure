# ADR-001: Choice of Operating System for firewall

## Status
Accepted (2026-02-15)

## Context
There is a need for a firewall that will secure the local network with service hosts and connect operators via VPN to that separate network. Currently, all hosts are connected to a basic home network, which is dangerous because any peer on the same LAN could reach services on those hosts.
We need a firewall operating system that provides the following features:
- free to use, as we do not have a budget for commercial products
- ability to create VLANs for separation of QA, PREPROD, and PROD networks
- VPN integration
- clean web interface
- easy backup system
- SSL certificate generation and secure storage for tooling on different hosts

Our bare-metal host for this OS is a Dell OptiPlex 3050 with 16 GB RAM, 128 GB NVMe SSD storage, and with a gigabit Ethernet quad-port NIC for VLANs.

## Options considered

### Option A: OpenWRT

+ Lightweight and flexible
+ Broad hardware compatibility and strong package ecosystem
- Better fit for small embedded routers than our use case
- Requires more manual setup for VLANs, VPN, and certificate handling
- Web UI is functional but less complete for daily operations

### Option B: OPNsense

+ Modern and consistent web interface
+ Strong built-in support for VLANs and VPN
+ Straightforward backup and restore workflow
+ Clear tooling for certificate management
+ Fully open source and free to use
- BSD system to learn

### Option C: pfSense

+ Mature and widely used platform
+ Strong firewall and VPN capabilities
+ Well-documented project
- UI and extension model quite obsolete
- Connected with Netgate, so product maintenance is more focused on paying customers
- BSD system to learn

#### Other options

We also found an OPNsense fork called [DynFi Firewall](https://dynfi.com/en/dynfi-firewall/) from a French company selling firewalls, but we skipped evaluating it because it is a fork of our chosen option.

## Decision

It was hard to decide between mature pfSense and OPNsense, but we chose **OPNsense** as the firewall operating system.
It satisfies all required capabilities (free usage, VLAN support, VPN integration, web administration, backup and restore, and certificate handling) and is also well regarded by the homelab community on Discord and Reddit.

## Consequences

- Positive: better network isolation through VLAN-based segmentation of QA, PREPROD, and PROD
- Positive: improved security posture by separating service hosts from the home LAN and enforcing firewall policy at a dedicated boundary
- Positive: simpler operations due to clear web UI, backup/restore process, and integrated VPN/certificate tooling
- Positive: no licensing cost for the chosen solution
- Negative: team needs initial familiarization with OPNsense administration and update procedures

## References

- [OPNsense documentation](https://docs.opnsense.org/)
- [OpenWRT documentation](https://openwrt.org/docs/start)
- [pfSense documentation](https://docs.netgate.com/pfsense/en/latest/)
