---
tags: [enum, snmp]
aliases: [SNMP]
---
Simple Network Management Protocol (SNMP) is used to monitor devices and handle configuration tasks and change settings remotely. SNMP transmits control commands using agents over UDP port **161**. It also used traps over UDP port **162** which are traps sent from the SNMP server to the client, usually when an event occurs.

Management Information Base (MiB) is a format for storing device information. Is a text file with SNMP objects listed as a standardized hierarchy. It contains atleast one Object Identifier (OID) which represents a node in a hierarchical namespace.

SNMPv3 which is the most modern and secure version.

# Community String Query

Community Strings are passwords that are used to determine wether the requested information can be viewed.

```shell
# Queri4es the OIDs for their info
snmpwalk -v2c -c public 10.129.14.128
```

# Community String Discovery

Use `onesixtyone` to identify valid community strings.

```shell
#Allows the idnetification of community strings
onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128
```

# OID Brute Force

Brute force the individual OIDs and enumerate the info behind them.

```shell
# brute force the individual OIDs and enumerate the info behind them
braa <community string>@<IP>:.1.3.6.*
```

# Dangerous SNMP Settings

|**Settings**|**Description**|
|---|---|
|`rwuser noauth`|Provides access to the full OID tree without authentication.|
|`rwcommunity <community string> <IPv4 address>`|Provides access to the full OID tree regardless of where the requests were sent from.|
|`rwcommunity6 <community string> <IPv6 address>`|Same access as with `rwcommunity` with the difference of using IPv6.|

## Related

- [[smb-attacks]]
- [[06-post-exploitation/credential-access/credential-hunting|Credential Hunting]]
- [[rdp]]
- [[network-services]]

