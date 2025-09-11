08.09.2025 11:32

We begin by downloading the Windows Server 2019 ISO. The trial provides 180 days of usage. After installation, it’s a good idea to take a VMware snapshot, then switch the network adapter to VLAN 20. Once that’s done, we assign a static IP address, rename the machine, and restart so the changes take effect.

![Asset17.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset17.png)  
![Asset18.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset18.png)  

---

## Roles and Features

From Server Manager, go to _Manage > Add Roles and Features_. Most steps can be skipped until the server roles screen, where we select **Active Directory Domain Services, DHCP Server, DNS Server, File and Storage Services,** and **Remote Access**. The installation may take a few minutes.

![Asset19.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset19.png)  

When it’s completed, we promote the server to a domain controller, create a new forest, and set the domain name.

![Asset20.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset20.png)  

After entering the administrator password and finalizing the wizard, the server restarts. If successful, the login screen confirms that the domain controller is ready.

![Asset21.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset21.png)  


---

## DHCP

With Active Directory in place, we move on to DHCP. From _Tools > DHCP_, right-click on IPv4 and create a new scope.
![Asset22.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset22.png)  

Give it a name and description, then define the IP range. Here, we’re using 51–151, leaving addresses outside that range reserved for servers and devices that need static IPs.

![Asset23.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset23.png)  
![Asset24.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset24.png)  

No exclusions are needed, and the lease duration can remain as default. Next, we configure the gateway, DNS, and WINS servers.

![Asset25.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset25.png)
![Asset26.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset26.png)
![Asset27.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset27.png)

Once finished, the address pool becomes visible.

![Asset28.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset28.png)

---

## DNS

DNS setup begins by creating a reverse lookup zone. From _Tools > DNS_, right-click **Reverse Lookup Zones** and add a new zone.

![Asset29.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset29.png) 

Skip the defaults until the Network ID step, where we enter `10.0.20`.

![Asset30.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset30.png)

Next, we add a forward lookup entry. In the **null.corp** zone, right-click and create a new host (A or AAAA). Enter the name and IP, check _Create associated pointer_, and save.

![Asset31.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset31.png) 

Finally, a quick test with `nslookup` verifies the configuration.

![Asset32.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset32.png)

---

## Static Routing

For SIEM integration, endpoints need to route logs to a specific IP. To achieve this, open _Routing and Remote Access_, right-click the domain controller (local), enable routing, and choose _Custom > LAN Routing_. Under IPv4 static routes, add a route pointing to the SIEM server at `10.0.50.55`.

![Asset33.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset33.png)
![Asset34.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset34.png)

This IP will later be handed off to the SIEM system.

---

## Users and Groups

Next, we create users and groups. Since this is a test environment, we’ll allow weak passwords after disabling Microsoft’s default restrictions. From _Active Directory Users and Computers_, create three users:

![Asset35.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset35.png)

Then, make a new organizational unit called _Groups_ and add three groups inside it: **HR, Marketing, and IT OPS**:

![Asset36.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset36.png) 

Assign the users to these groups, and promote one user to admin.

---

## Local Admin Policy

To ensure proper workstation management, we configure a local admin policy. In _Group Policy Management_, create a new GPO named _Local Admin GPO_.:

![Asset37.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset37.png)

Edit it under _Windows Settings > Security Settings > Restricted Groups_ and create a group called _Local Administrators – Workstations_:

![Asset38.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset38.png) 

Add the **IT OPS** group to this policy for administrative access.

---

## Shared Folders

Finally, we set up shared resources. On the C: drive, create the required folders, then assign permissions so each group has the appropriate read/write access.

![Asset39.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset39.png)
![Asset40.png](https://github.com/v3n1x/Projects/blob/main/Assets/Asset40.png)

---

With that, the domain controller is fully configured.
