# Kali Linux — External Network Attack Lab


---

## Installation and configuration

Imported the official Kali VMware appliance (OVA/VMX) into VMware Workstation/Player using default settings. Booted the VM and updated packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Set a static IP for the Kali VM on the external/attack network (example: `10.0.10.50/24`, gateway `10.0.10.254`).

Took a snapshot (`fresh_kali_pre_nmap`) before making network or firewall changes.

---

## Network

Assigned the Kali VM network adapter to `VMnet10` to represent the external network. Confirm connectivity to the firewall WAN (`10.0.10.254`) and ensure routing/Gateway are correct.

Commands used for verification:

```bash
ip addr show
ping -c 3 10.0.10.254
```

---

## Firewall configuration

On the firewall (pfSense-like), added NAT/port-forward rules on the WAN (VLAN 10) to forward traffic to the internal host `10.0.20.10`.

Port mappings configured:

- WAN:80  → 10.0.20.10:80
- WAN:3306 → 10.0.20.10:3306
- WAN:4422 → 10.0.20.10:22

Ensure corresponding firewall allow rules exist for the forwarded ports.

---

## Test connectivity

From Kali (external side) verify services via the firewall WAN IP (`10.0.10.254`):

- HTTP: `http://10.0.10.254` — DVWA should load.
- SSH: `ssh -p 4422 user@10.0.10.254` — should prompt for credentials.

Useful scan command:

```bash
ping 10.0.10.254
```

If tests fail, check: VMnet10 settings, firewall interface selection (VLAN 10), NAT target IP (`10.0.20.10`), and that the internal server is up.

---

And we're done. We now have a ready malicious machine for testing attacks on the Null network.

