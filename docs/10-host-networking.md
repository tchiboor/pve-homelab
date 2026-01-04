# Proxmox Host Networking (pve-lab)

This document describes the network configuration of the Proxmox VE host and establishes the foundation for lab isolation and controlled connectivity.

---

## Goal

- `vmbr0` — Management and LAN access (`192.168.1.0/24`)
- `vmbr1` — Isolated lab network (`10.10.10.0/24`)
- No routing between networks unless explicitly configured  
  (NAT is introduced in a later step)

---

## Current Design

- **Host**: `pve-lab`
- **Proxmox VE**: 9.1.4

### `vmbr0` — Management / LAN Bridge

- IP address: `192.168.1.100/24`
- Default gateway: `192.168.1.1`
- Bridge ports: `nic0`
- Purpose:
  - Proxmox management access
  - Upstream LAN and internet connectivity

### `vmbr1` — Lab Bridge

- IP address: `10.10.10.1/24`
- No gateway configured on the bridge  
  (the Proxmox host acts as the gateway for lab VMs)
- No physical NIC attached
- Purpose:
  - Isolated network segment for lab workloads
  - Default gateway for VMs attached to `vmbr1`

---

## Verification

Run the following commands on the Proxmox host to verify bridge and routing state:

```bash
ip a | egrep "vmbr0|vmbr1|nic0"
ip r
```

## Expected results:

- Both vmbr0 and vmbr1 are in the UP state
- vmbr0 has the default route via 192.168.1.1
- vmbr1 has a connected route for 10.10.10.0/24
- No default gateway is associated with vmbr1