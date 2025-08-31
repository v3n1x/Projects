
### pfSense Setup

**pfSense** is a free, open-source firewall that can also function as a router. In this project, it will serve as our firewall, installed on a virtual machine with the following specifications:

- **CPU:** 1
    
- **RAM:** 512 MB
    
- **HDD:** 30 GB
    
- **NICs:** 6
    
    - Bridged
        
    - vmnet1
        
    - vmnet10
        
    - vmnet20
        
    - vmnet50
        
    - vmnet99
        

Since we are working with **5 networks**, we’ll assign interfaces accordingly. The **bridged interface** provides internet access to the security network.

#### Installation

The pfSense installation is performed with **default settings**. After installation, pfSense boots up with two configured interfaces: **WAN** and **LAN**.

Once installed:

- Assign the correct IP addresses.
    
- Set up management access.
    
- Access the WebConfigurator at **10.0.1.50** with the default credentials:
    
    - **Username:** `admin`
        
    - **Password:** `pfsense`
        

The setup wizard will prompt you to configure:

- Domain name and hostname.
    
- DNS servers (e.g., `8.8.8.8` and `8.8.4.4`).
    
- Protocol (change to **HTTPS** under _System > General Settings_).
    
- Enable **SSH** for secure shell access.
    

#### Interface Configuration

Navigate to **Interfaces > Interface Assignments** and add all interfaces. For each interface, configure the following:

- **Enable:** Checked
    
- **Description:** `$NETWORK_NAME_VLAN<VLAN_NUMBER>`
    
- **IPv4 Configuration Type:** Static IPv4
    
- **IPv6 Configuration Type:** None
    
- **MAC Address:** Leave empty
    
- **MTU / MSS:** Leave empty
    
- **Speed and Duplex:** Default
    
- **IPv4 Address:** Assign from the predefined table, with subnet mask `/24`.
    
- **IPv4 Upstream Gateway:** _None_ (except WAN, which requires a gateway).
    
- **Block private networks:** Unchecked (checked only for WAN).
    
- **Block bogon networks:** Unchecked (checked only for WAN).
    

---

### Firewall Rules Configuration

- **WAN**  
    Leave this as default — no changes required.

![Asset03.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset03.png)

    
- **Management**  
    Same as above — no modifications needed.
    
![Asset04.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset04.png)

- **Corporate_WAN_VLAN10**  
    Allow traffic within VLAN 10 and permit traffic going to VLAN 20.

![Asset05.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset05.png)

- **Corporate_LAN_VLAN20**  
    Apply the same rules as VLAN 10, since this network will simulate access to the fake internet.

![Asset06.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset06.png)

- **Security_VLAN50**  
    Block access to **WAN**, **Management**, and **Corporate_WAN_VLAN10**.  
    Allow traffic to all other destinations (Internet, Isolated VLAN, and Corporate LAN).

![Asset07.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset07.png)

- **Isolation VLAN**  
    Block all traffic entirely.

![Asset08.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset08.png)

After we're done, we should get an interface that looks like this:

![Asset09.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset09.png)

Below is a screenshot from the pfSense VM in VMware, showing the assigned IP addresses for each interface (WAN, Management, Corporate VLANs, Security, and Isolation).

![Asset11.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset11.png)

---
### Outbound NAT Configuration

pfSense automatically creates some default outbound NAT rules. In this case, the first four entries under **WAN** (for `127.0.0.0/8` and `::1/128`) were left as-is — these are default mappings that don’t need modification.

For the rest, I configured outbound NAT rules for my networks as follows:

- **WAN (10.0.1.0/24)**
    
    - One rule for **all traffic**.
        
    - One rule specifically for **destination port 500 (ISAKMP)**.
        
- **Corporate_WAN_VLAN10 (10.0.10.0/24)**
    
    - One rule for **all traffic**.
        
    - One rule specifically for **destination port 500 (ISAKMP)**.
        
- **Corporate_LAN_VLAN20 (10.0.20.0/24)**
    
    - One rule for **all traffic**.
        
    - One rule specifically for **destination port 500 (ISAKMP)**.
        

This structure ensures that both general outbound connections and ISAKMP (VPN-related) traffic are correctly translated for each VLAN.

To set it up we need to navigate to:  
**Firewall > NAT > Outbound**  

The final outbound NAT configuration should look like this:

![Asset10.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset10.png)

---

With the firewall configuration complete, we can now move on to adding machines to the network. 
