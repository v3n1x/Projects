08.09.2025

We begin by downloading the Windows Server 2019 ISO. The trial provides 180 days of usage. After installation, it’s a good idea to take a VMware snapshot, then switch the network adapter to VLAN 20. Once that’s done, we assign a static IP address, rename the machine, and restart so the changes take effect.
![screenshot17],
![screenshot18]

---

## Roles and Features

From Server Manager, go to _Manage > Add Roles and Features_. Most steps can be skipped until the server roles screen, where we select **Active Directory Domain Services, DHCP Server, DNS Server, File and Storage Services,** and **Remote Access**. The installation may take a few minutes ![screenshot19].

When it’s complete, we promote the server to a domain controller, create a new forest, and set the domain name ![screenshot20]. After entering the administrator password and finalizing the wizard, the server restarts. If successful, the login screen confirms that the domain controller is ready ![screenshot21].

---

## DHCP

With Active Directory in place, we move on to DHCP. From _Tools > DHCP_, right-click on IPv4 and create a new scope ![screenshot22]. Give it a name and description ![screenshot23], then define the IP range. Here, we’re using 51–151, leaving addresses outside that range reserved for servers and devices that need static IPs ![screenshot24].

No exclusions are needed, and the lease duration can remain as default. Next, we configure the gateway, DNS, and WINS servers ![screenshot25] ![screenshot26] ![screenshot27]. Once finished, the address pool becomes visible ![screenshot28].

---

## DNS

DNS setup begins by creating a reverse lookup zone. From _Tools > DNS_, right-click **Reverse Lookup Zones** and add a new zone ![screenshot29]. Skip the defaults until the Network ID step, where we enter `10.0.20` ![screenshot30].

Next, we add a forward lookup entry. In the **null.corp** zone, right-click and create a new host (A or AAAA). Enter the name and IP, check _Create associated pointer_, and save ![screenshot31]. Finally, a quick test with `nslookup` verifies the configuration ![screenshot32].

---

## Static Routing

For SIEM integration, endpoints need to route logs to a specific IP. To achieve this, open _Routing and Remote Access_, right-click the domain controller (local), enable routing, and choose _Custom > LAN Routing_. Under IPv4 static routes, add a route pointing to the SIEM server at `10.0.50.55` ![screenshot33] ![screenshot34]. This IP will later be handed off to the SIEM system.

---

## Users and Groups

Next, we create users and groups. Since this is a test environment, we’ll allow weak passwords after disabling Microsoft’s default restrictions. From _Active Directory Users and Computers_, create three users ![screenshot35].

Then, make a new organizational unit called _Groups_ and add three groups inside it: **HR, Marketing, and IT OPS** ![screenshot36]. Assign the users to these groups, and promote one user to admin.

---

## Local Admin Policy

To ensure proper workstation management, we configure a local admin policy. In _Group Policy Management_, create a new GPO named _Local Admin GPO_ ![screenshot37]. Edit it under _Windows Settings > Security Settings > Restricted Groups_ and create a group called _Local Administrators – Workstations_ ![screenshot38]. Add the **IT OPS** group to this policy for administrative access.

---

## Shared Folders

Finally, we set up shared resources. On the C: drive, create the required folders ![screenshot39], then assign permissions so each group has the appropriate read/write access ![screenshot40].

---