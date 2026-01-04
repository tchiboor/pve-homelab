# Lab Overview

This repository documents a Proxmox VE homelab deployed on a Minisforum UM890, designed to support isolated experimentation and service testing in a controlled environment.

The lab is intentionally structured to be **reproducible**, **minimally exposed**, and **easy to reason about**, with clear separation between management access and lab workloads.

---

## Hardware

- **Minisforum UM890**

---

## Hypervisor

- **Proxmox VE**: 9.1.4

---

## Host

- **Hostname**: `pve-lab`

---

## Virtual Machines

Primary lab workload:

- **Name**: `openg2p`
- **OS**: Ubuntu Server 22.04 LTS

Additional VMs may be created from templates as the lab evolves.

---

## Goal

Provide an isolated and reproducible lab environment with controlled outbound internet access for running and testing OpenG2P services, without exposing lab workloads directly to the LAN.

---

## Network Architecture

The lab uses a **dual-bridge Proxmox networking model** with an isolated, NATed lab network.

- **`vmbr0`**
  - Management and LAN-facing bridge
  - Provides host access and upstream internet connectivity

- **`vmbr1`**
  - Isolated lab bridge with no physical NIC attached
  - Serves as the default gateway for lab VMs

- The Proxmox host performs **NAT** to allow outbound-only internet access from the lab network.
- No routing exists between bridges unless explicitly configured.

This design ensures lab workloads remain isolated while still being able to reach external resources when required.
