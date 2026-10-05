
Dynamic Host Configuration Protocol (DHCP) facilitates the communication of users and systems on the network by assigning IP addresses. 

Attackers can add their own DHCP server to the network and create malicious network configurations, and snoop into information exchange. 

# MAC Filtering

Control whether devices with a specific MAC can receive an IP, this is needed when only certain devices on the network can receive an IP.

After opening "Server Manager", select "DHCP" from the "Tools" menu. On the DHCP Management screen that opens, from the “IPv4” -> “Filter” section, right-click on the “Allow” or “Deny” section and select “New Filter”.

# Rogue DHCP Blocking

Unauthorized DHCP server starts serving on a network.

To use DHCP server authorization, the DHCP server must be in an Active Directory domain. To authorize the DHCP server, open the DHCP console, right-click the DHCP server and click "Authorize"

We can see DHCP logs under the "C:\Windows\system32\dhcp".

Some events to keep track of

![[Pasted image 20261002163730.png]]

![[Pasted image 20261002163735.png]]