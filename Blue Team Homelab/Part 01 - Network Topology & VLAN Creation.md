30.08.2025 13:47

This project focuses on building a complete blue team homelab environment using virtualization. The setup includes firewalls, network topology, antivirus, SIEM, EDR, and other security tools. To achieve segmentation and security, the environment is divided into multiple VLANs:

VLAN 1 (Management): Dedicated to administrative access to the firewall.

VLAN 10 (Corporate WAN): Hosts a Kali Linux machine to simulate external attacks. This VLAN is isolated from both the real internet and the host machine, with access only to VLAN 20.

VLAN 20 (Corporate LAN): Contains servers and endpoint devices. This network only communicates with the simulated internet environment.

VLAN 50 (Security): Houses security tools with internet connectivity for retrieving public resources and also connects to the corporate network for centralized log collection and analysis.

VLAN 99 (Malware Analysis): An isolated environment designed for safe malware testing, hosted under the domain null.corp (NULL CORP).

**Here is the Network Information:**

| Name          | Domain    | VLAN | Subnet       | Gateway     |
| ------------- | --------- | ---- | ------------ | ----------- |
| Management    | N/A       | 1    | 10.0.1.0/24  | 10.0.1.1    |
| Corporate WAN | N/A       | 10   | 10.0.10.0/24 | 10.0.10.254 |
| Corporate LAN | null.corp | 20   | 10.0.20.0/24 | 10.0.20.254 |
| Security      | null.corp | 50   | 10.0.50.0/24 | 10.0.50.254 |
| Isolated LAN  | N/A       | 99   | 10.0.99.0/24 | 10.0.99.254 |

**And here is the Network Topology:**

![[Topology.png]]

**VLAN Creation:** 

The following virtual networks are defined within **VMware Virtual Network Editor**:

- **vmnet10** – Host-only
    
- **vmnet20** – Host-only
    
- **vmnet50** – Host-only
    
- **vmnet99** – Host-only
    

For each network, the option **“Use local DHCP”** is disabled, as the firewall will act as the DHCP server.  
Subnet IPs are assigned according to the addressing scheme defined in the table previously displayed.

#### Final Configuration Overview

All four networks are configured as **Host-only**, with DHCP disabled, and subnet ranges applied based on the lab design.

Here is the final result: 

![[VLAN CREATION.png]]

### Tools Utilized

- **VMware Workstation** – for building and managing virtual machines and VLANs
    
- **Draw.io** – for creating diagrams, including the network topology
    
- **pfSense** – open-source router and firewall solution
    
- **Virtual Machines** – used throughout the lab environment
    
- **Notion & Obsidian** – for maintaining notes in Markdown format

### Next Steps

With the virtual networks created and subnets assigned, the following stage involves configuring **pfSense** and defining the firewall rules for the environment.