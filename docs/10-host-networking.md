# Proxmox Host Networking (pve-lab)

## Goal
- `vmbr0` = management + LAN access (192.168.1.0/24)
- `vmbr1` = isolated lab network (10.10.10.0/24)
- No routing between networks unless explicitly configured (we do NAT later)

## Current Design
- Host: `pve-lab`
- Proxmox VE: 9.1.4
- `vmbr0` (WAN/LAN side)
  - IP: 192.168.1.100/24
  - Gateway: 192.168.1.1
  - Bridge ports: `nic0`
- `vmbr1` (Lab side)
  - IP: 10.10.10.1/24
  - No gateway set on the bridge (it IS the gateway for lab VMs)

## Verify
On host:
```bash
ip a | egrep "vmbr0|vmbr1|nic0"
ip r
