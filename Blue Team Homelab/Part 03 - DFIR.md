
## SIFT Workstations

SIFT Workstation is a DFIR-focused operating system that comes preloaded with a wide range of tools and cheat sheets. We can download the OVA file from [here](https://www.sans.org/tools/sift-workstation), so there’s no need to set up everything manually. In our setup, this machine will act as the security team’s workstation. After importing the OVA, we connect it to VLAN 50 to give it access to the corporate LAN, as shown in the picture below:

![[Asset16.png]]

Once the machine is running, we can log in using the default credentials.
Once logged in, the first thing we want to do is secure the machine by changing its password using the `passwd` command. At the same time, it’s a good idea to update the machine’s name, or hostname, by editing both `/etc/hostname` and `/etc/hosts`. Below is the picture that shows how it should look like:

![[Asset12 1.png]]

After that, we need to set a static IP address for the VM. We can do this by searching for **Advanced Network Configuration**. If there isn’t an existing connection, we add a new one and choose **Ethernet**, then edit the IPv4 settings to configure the IP address, netmask, gateway, DNS servers, and search domains (null.corp in my case).

![[Asset13.png]]

To make sure the hostname resolves correctly on the network, we also need to create a host override record on our firewall. This can be done by going to **Services > DNS Resolver > General Settings**. Scroll down to the **Host Override** section, click **Add**, fill in the correct information, and then click **Save**. Below is the picture that shows how to do it:

![[Asset14.png]]

Finally, it’s important to verify that the machine has network connectivity and that DNS is working properly. We can do this from the GUI by navigating to **Diagnostics > Ping** and typing the hostname of the VM, which in my case is `dfir.null.corp`. As shown in the picture below, successful pings confirm that the DNS is resolving correctly and the machine is reachable:

![[Asset15.png]]

With that, the DFIR workstation is fully set up. Later, we can proceed to configure malware analysis VMs and SIEM solutions.
