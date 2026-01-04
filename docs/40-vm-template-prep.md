# VM Template Preparation

This document describes the steps used to prepare an Ubuntu base VM before converting it into a reusable Proxmox template.

The goal is to create a clean, minimal, cloud-init–ready image suitable for cloning lab workloads.

---

## Base VM

- OS: Ubuntu Server 22.04 LTS
- Installation method: ISO install
- Disk: Single virtio disk
- Network: Temporary attachment to `vmbr0` for setup and updates

---

## Installed Components

```bash
apt update
apt install -y \
  cloud-init \
  qemu-guest-agent \
  curl \
  wget \
  vim \
  net-tools \
  ca-certificates
```

## Guest Agent
Enable and verify the QEMU guest agent:
```bash
systemctl enable qemu-guest-agent
systemctl start qemu-guest-agent
systemctl status qemu-guest-agent
```
## Cloud-Init Preparation

Ensure cloud-init is installed and enabled:
```bash
systemctl enable cloud-init
systemctl status cloud-init

##Clean cloud-init state before templating:

cloud-init clean
```
## System Cleanup

Remove machine-specific state:
```bash
truncate -s 0 /etc/machine-id
rm -f /var/lib/dbus/machine-id
ln -s /etc/machine-id /var/lib/dbus/machine-id

##Optional cleanup:

apt clean
rm -rf /tmp/*
rm -rf /var/tmp/*
```

## Snapshot
-Take a snapshot before converting to a template:
- Name: pre-template

## Conversion to Template
1. Power off the VM
2. Convert the VM to a Proxmox template
3. Template name: ubuntu-22.04-cloudinit
4. Configure cloud-init in Proxmox:
    - Networking: DHCP

## Post-Template Notes
- Do not boot the template directly
- Always clone from the template to create new VMs
- Attach lab VMs to vmbr1 unless LAN access is explicitly required