# Addressing Plan

This file lists the networks, hosts, IP addresses and allowed flows of the lab, so the environment can be rebuilt without reading the rest of the repository. The lab runs on VMware.


## 1. Networks

| Network | CIDR | VMware network type | Purpose |
|---|---|---|---|
| External | 192.168.56.0/24 | Host-only | Client to bastion |
| Internal | 10.0.0.0/24 | LAN Segment named `INTERNAL` | Bastion to internal server, isolated |
| NAT | Assigned by VMware (DHCP) | NAT | Package installation only, disconnected afterwards |

- Both lab networks use private addresses (RFC 1918). Each /24 offers 254 usable addresses.
- No default gateway is configured on the External and Internal networks: nothing is routed between them.
- The NAT adapter provides a temporary default route during installation. It is disconnected on all three machines once the setup is complete.

## 2. Hosts and interfaces

| Host | OS | Interface | Network | IP / mask | Role |
|---|---|---|---|---|---|
| Client | Kali Linux | `<eth0>` | External | 192.168.56.5/24 (static) | Administration workstation |
| Client | Kali Linux | `<eth1>` | NAT | DHCP (temporary) | Installation only |
| Bastion | Ubuntu Server 22.04 | `<ens37>` | External | 192.168.56.10/24 (static) | SSH entry point |
| Bastion | Ubuntu Server 22.04 | `<ens38>` | Internal | 10.0.0.10/24 (static) | Relay to the internal server |
| Bastion | Ubuntu Server 22.04 | `<ens33>` | NAT | DHCP (temporary) | Installation only |
| Internal server | Ubuntu Server 22.04 | `<ens37>` | Internal | 10.0.0.20/24 (static) | Protected target |
| Internal server | Ubuntu Server 22.04 | `<ens33>` | NAT | DHCP (temporary) | Installation only |

Interface names can be read with `ip -br a` on each machine.

## 3. Allowed flows

| Source | Destination | Port | Status | Enforced by |
|---|---|---|---|---|
| Client | Bastion | TCP 22 | Allowed | `ufw` on the bastion (from 192.168.56.0/24) |
| Bastion | Internal server | TCP 22 | Allowed | `PermitOpen` on the bastion, `ufw` on the internal server (from 10.0.0.10) |
| Client | Internal server | any | Denied | No network path between the External and Internal networks |
| Bastion | Other internal hosts or ports | any | Denied | `PermitOpen 10.0.0.20:22` |
| Any | Internal server | TCP 22 | Denied, except from 10.0.0.10 | `ufw` on the internal server |

## 4. Notes

- The bastion does not route packets: `net.ipv4.ip_forward` is `0`. It relays SSH connections at the application level only.
- Addresses are static on the lab networks. Only the temporary NAT adapters use DHCP.
- Configuration details are kept in their own files: SSH settings and firewall rules are in the `configs/` directory.