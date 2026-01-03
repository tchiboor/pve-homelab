# Lab Overview

This repository documents a Proxmox VE homelab running on a Minisforum UM890.

## Hardware
- Minisforum UM890

## Hypervisor
- Proxmox VE 9.1.4

## Host
- Hostname: pve-lab

## VM
- Name: openg2p
- OS: Ubuntu Server 22.04

## Goal
Provide an isolated, reproducible lab environment with controlled internet access
for OpenG2P services.

## Network Architecture

The lab uses a dual-bridge Proxmox networking model with an isolated NATed lab network.

- `vmbr0` provides management access and upstream connectivity.
- `vmbr1` is an isolated lab network with no direct LAN exposure.
- Proxmox performs NAT to allow outbound-only internet access for lab workloads.
- No routing exists between networks unless explicitly configured.
