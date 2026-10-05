
DNS translates domain name in to corresponding IP addresses when a URL is entered in a web browser. Attackers can manipulate DNS queries and redirect users to fake sites (DNS poisoning or spoofing). 

Windows DNS Server is a service provided by Microsoft and is typically used by Windows-based networks. 

# Zone Transfer Restriction

DNS Zone Transfer is the process of copying all DNS records from the primary DNS server (master) to the second DNS servers (slave or backup). 

DNS data is structured into a zone containing all records of a particular DNS namespace. Two types of zone transfers exist:
- Full Zone Transfer (AXFR) copies all information of a zone from the primary DNS server to the backup.
- Incremental Zone Transfer (IXFR) copies changes in a zone from the primary DNS to the backup.

To restrict zone transfers we can:
Open the DNS management console. To do this, open "Server Manager", then select the "Tools" menu and click "DNS". On the screen that opens, right-click on the relevant zone and click on “Properties”. Then switch only allow Zone Transfer to restricted servers.

# DNSSec

Standards to verify the integrity and authenticity of DNS queries and responses. To activate, open the "Server Manager", then select the "Tools" menu and click on the "DNS" option. Right-click on the zone you will apply and click “DNSSEC” -> “Sign the Zone”.

--- 

Logs for DNS will appear as such:

![[Pasted image 20261002162210.png]]

