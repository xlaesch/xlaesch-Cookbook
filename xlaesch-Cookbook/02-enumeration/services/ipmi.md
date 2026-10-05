---
tags: [enum, linux, ipmi]
aliases: [IPMI]
---
Intelligent Platform Management Interface (IPMI) is a set of standardized specifications for hardware-based host management systems and monitoring. It operates independently of the host's BIOS, CPU, firmware, etc. It operates via a direct connection to the system's hardware and does not require access to the operating system via login shell.

Communicates over UDP port 623. Systems that use IPMI are called Baseboard Management Controllers (BMC). Gaining access to a BMC allows full access to the host motherboard, and we would be able to reinstall the host operating system.

# Service Footprint

Start by checking whether UDP/623 exposes IPMI.

```shell
# Footprint the service
sudo nmap -sU --script ipmi-version -p 623 
```

# Metasploit Version Scanner

Metasploit also has a scanner module.

```shell
# metasploit also has a scanner module
msf > use auxiliary/scanner/ipmi/ipmi_version 
```

# IPMI Hash Retrieval

IPMI 2.0 has a flaw that allows for the salted hash to be obtained for any valid user account in the BMC.

```shell
# IPMI 2.0 SHA1 Password hash retrieval
msf > use auxiliary/scanner/ipmi/ipmi_dumphashes
```

# Default Passwords

Default IPMI passwords will often stay the same.

|Product|Username|Password|
|---|---|---|
|Dell iDRAC|root|calvin|
|HP iLO|Administrator|randomized 8-character string consisting of numbers and uppercase letters|
|Supermicro IPMI|ADMIN|ADMIN|

## Related

- [[wmi]]
- [[rdp]]
- [[snmp]]
- [[winrm]]

