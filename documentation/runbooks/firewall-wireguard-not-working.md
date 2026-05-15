# Runbook: WireGuard relay tunnel on firewall does not work

## Status

Created on 2026-04-03

## When to use

DNS resolution fails, internal services are unreachable through the tunnel, and the firewall does not respond to pings in its role as LAN relay (symptoms seen from the WireGuard side or when crossing from WG into `10.0.0.0/24`).

## Requirements

- Physical access to the firewall host or connectivity to the internal LAN `10.0.0.0/24` (SSH is temporarily open on port 22 on the LAN interface only; it will be closed later for security reasons)
- Root password

## Problem verification

Pings that should traverse WireGuard: e.g. from a remote peer toward hosts on the **internal LAN** (`10.0.0.0/24`) or toward the firewall’s WireGuard address (`10.1.1.2` on the QA WireGuard segment `10.1.1.0/24`) show **100% ICMP loss**.

## Problem cause

**Suspected race on boot:** the WireGuard service may start before firewall rules are fully applied, so tunnel traffic is dropped until WireGuard is restarted. Treat this as the working hypothesis behind the recovery steps below unless logs show another fault.

## Procedure

### Step 1: log in to firewall host as root

In your terminal run `ssh root@bouncer.hackbox.cc` or `ssh root@10.0.0.1` (if DNS does not work), or log in on the firewall console and choose option `8` for shell access.

### Step 2: restart WireGuard service

Run `configctl wireguard restart` to restart WireGuard. The command should report `OK`.

### Step 3: check if WireGuard is running

Run `wg show` to confirm peers are connected and inspect the last handshake time. Then ping `10.1.1.1` (the remote WireGuard peer on Mikrus, opposite Bouncer’s `10.1.1.2`) to confirm the tunnel is stable.

## Troubleshooting

If WireGuard still fails and the OPNsense GUI is reachable, open `Lobby -> Dashboard` and confirm WireGuard shows a healthy (green) status. If not, inspect `VPN -> WireGuard -> Log File`. **Unbound** (DNS) may also have crashed, so restart the Unbound DNS service if lookups fail.

## Current temporary solution

A cron job restarts WireGuard every hour so that after a power outage the service may recover without you having to log in to the firewall from the LAN.
