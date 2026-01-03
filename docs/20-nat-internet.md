# NAT Internet for Lab Network (vmbr1 -> vmbr0)

## Goal
Allow VMs on `10.10.10.0/24` (vmbr1) to reach the internet through Proxmox host on `vmbr0`.

### Requirements
- Host can reach internet via vmbr0
- Enable IPv4 forwarding
- Add NAT masquerade rule
- Allow forwarding between vmbr1 and vmbr0
- Persist rules across reboot

---

## 1) Enable IPv4 forwarding (persistent)
```bash
echo 'net.ipv4.ip_forward=1' > /etc/sysctl.d/99-lab-nat.conf
sysctl --system
sysctl net.ipv4.ip_forward
```
## 2) Add NAT + Forward rules (iptables)
```bash
iptables -t nat -A POSTROUTING -s 10.10.10.0/24 -o vmbr0 -j MASQUERADE
iptables -A FORWARD -i vmbr1 -o vmbr0 -j ACCEPT
iptables -A FORWARD -i vmbr0 -o vmbr1 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
```
### Verify:

```bash
iptables -t nat -S | grep MASQUERADE
iptables -S FORWARD
```
## 3) Persist iptables rules
```bash
apt update
apt install -y iptables-persistent
```

## 4) VM-side requirements
VM NIC must be attached to vmbr1.

VM network must have:

- IP: 10.10.10.X/24
- Default gateway: 10.10.10.1
- DNS: 1.1.1.1 and/or 8.8.8.8

### VM Verification
```bash
ip a
ip r
ping -c 2 10.10.10.1
ping -c 2 8.8.8.8
ping -c 2 google.com
```

## Common Mistakes
- Missing default route inside VM (gateway not set)
- NAT rule exists but FORWARD policy blocks
- Proxmox firewall rules override iptables (if enabled without correct rules)