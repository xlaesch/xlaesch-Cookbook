DNS translates domain names into IP addresses. Windows DNS Server provides this service on Windows-based networks; attackers can manipulate DNS queries to redirect users to fake sites through poisoning or spoofing.

# Zone Transfer Restriction

A zone contains the records of a DNS namespace. Zone transfers copy records from a primary DNS server to secondary servers.

| **Transfer** | **Description** |
| --- | --- |
| AXFR | Full zone transfer; copies all zone information from the primary server to a backup. |
| IXFR | Incremental zone transfer; copies changes from the primary server to a backup. |

Open **Server Manager → Tools → DNS**, right-click the relevant zone, then select **Properties**. Allow zone transfers only to restricted servers.

# DNSSEC

DNSSEC verifies the integrity and authenticity of DNS queries and responses.

Open **Server Manager → Tools → DNS**, right-click the relevant zone, then select **DNSSEC → Sign the Zone**.

# Logs

DNS logs appear as follows:

![[Pasted image 20261002162210.png]]
