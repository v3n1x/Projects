# Kali Linux — External Network Attack Lab


---

## Installation and configuration

Download the official Kali VMware appliance (OVA/VMX) from [here](https://www.kali.org/get-kali/#kali-virtual-machines) into VMware Workstation/Player using default settings. then update and upgrade the packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Don't forget to take a snapshot before making network or firewall changes.

---

## Network

Assigned the Kali VM network adapter to `VMnet10` to represent the external network. Then we set a static IP for the Kali VM on the external/attack network (example: `10.0.10.66/24`, gateway `10.0.10.254`) as shown in the picture below:

![Asset54.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset54.png)  

---

## Firewall configuration

On the firewall (pfSense), we need to set up 'Port Forward' rules on the WAN (VLAN 10) by heading to **Firewall > NAT > Port Forward**  

Port mappings configured:

- WAN:3306  → 10.0.20.10:3306 (MariaDB)
- WAN:80 → 10.0.20.10:80 (HTTP)
- WAN:4422 → 10.0.20.10:22 (SSH)

![Asset55.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset55.png)  

---

## Connectivity

Now let's try to access the DVWA from the Kali Linux machine (external side) by going to _`http://10.0.10.254`_:

![Asset56.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset56.png)  

Since it has worked, let's try to SSH the server

![Asset57.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset57.png)  

---

And we're done. We now have a ready malicious machine for testing attacks on the Null network.

