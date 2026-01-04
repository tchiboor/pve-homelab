# pve-homelab

Proxmox VE homelab running on a Minisforum UM890, designed as an **isolated, reproducible lab environment** with controlled internet access for test workloads.

---

## Overview

This repository documents the setup of a Proxmox VE host (`pve-lab`) using a **dual-bridge networking model**:

- `vmbr0` — Management + LAN / upstream internet
- `vmbr1` — Isolated lab network with outbound-only internet via NAT

The goal is to safely run lab services (OpenG2P and related experiments) without exposing them directly to the LAN.

---

## Environment

- **Hardware**: Minisforum UM890  
- **Hypervisor**: Proxmox VE 9.1.4  
- **Host OS**: Proxmox (Debian-based)  
- **Hostname**: `pve-lab`

---

## Network Design (High Level)

- `vmbr0`
  - Connected to physical NIC (`nic0`)
  - LAN subnet: `192.168.1.0/24`
  - Provides management access and upstream internet

- `vmbr1`
  - No physical NIC attached
  - Lab subnet: `10.10.10.0/24`
  - Acts as the **default gateway** for lab VMs
  - Internet access provided via NAT on the Proxmox host

No routing exists between networks unless explicitly configured.

---

## Repository Structure

```text
configs/
  pve-interfaces.example     # Example Proxmox network configuration

docs/
  00-overview.md             # Lab overview and goals
  10-host-networking.md      # Proxmox host networking (vmbr0/vmbr1)
  20-nat-internet.md         # NAT setup for lab internet access
  30-troubleshooting.md     # Connectivity troubleshooting
  40-vm-template-prep.md    # Ubuntu VM templating & cloud-init prep

README.md
```
## Quick Start (High Level)

1. Configure Proxmox networking (`vmbr0` and `vmbr1`)
2. Verify host connectivity on `vmbr0`
3. Enable IPv4 forwarding on the host
4. Configure NAT from `vmbr1` → `vmbr0`
5. Attach VMs to `vmbr1` and verify outbound internet access

Detailed steps are documented in the `docs/` directory.

---

## Assumptions

- Your LAN uses `192.168.1.0/24`
- Your upstream gateway is `192.168.1.1`
- Lab subnet is `10.10.10.0/24`

Proxmox host IPs:

- `vmbr0`: `192.168.1.100`
- `vmbr1`: `10.10.10.1`

You have console or physical access to the host in case of network misconfiguration.

Adjust addresses as needed for your environment.

---

## Safety Notes

- Misconfigured bridges, NAT, or firewall rules can lock you out of the host.
- Always keep console or physical access available when modifying networking.
- Proxmox firewall rules may override raw `iptables` rules if enabled—this is documented where relevant.

---

## Status

- Dual-bridge networking: ✅
- NAT for lab internet access: ✅
- VM templating with cloud-init: ✅
- Documentation: In progress and evolving

---

## Why This Exists

This repo serves as:

- A personal runbook
- A repeatable reference for rebuilding the lab
- A learning artifact for Proxmox networking, NAT, and VM lifecycle management
