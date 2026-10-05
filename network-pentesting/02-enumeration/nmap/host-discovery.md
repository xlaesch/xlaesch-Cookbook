---
tags: [enum, linux, nmap]
aliases: [Host Discovery]
---
To conduct a penetration test we have to get an overview of which systems are online, and which ones we can work with.

# Network Sweep

Use this when you have a CIDR range and need live hosts without scanning ports.

```shell
# scan a target network range without scanning ports (-sn) and storing the result in all formats starting with the name 'tnet'
sudo nmap $HOST/24 -sn -oA tnet | grep for | cut -d" " -f5
```

# Host List Sweep

Use a predefined host list when targets were gathered from OSINT, DNS, or prior enumeration.

```shell
# perform host discovery on a predefined list
sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5
```

# ICMP Probe

ICMP echo requests can confirm a single host is alive when ICMP is allowed.

```shell
# check whether a single IP is alive using ICMP echo requests (-PE) and packet tracing for more verbose output
sudo nmap $HOST -sn -oA host -PE --packet-trace 
```

# Routed Host Probe

Disable ARP pings when scanning across routers because ARP is local-only.

```shell
# check whether a single IP is alive without using ARP pings
sudo nmap $HOST -sn -oA host -PE --packet-trace --disable-arp-ping 
```

Some options during scanning can have advantages:
* **Disable DNS Resolution** saves time by skipping reverse DNS lookups
* **Disable ARP Ping** is better because ARP is local-only, if you're scanning across routers ARP won't work.
* **Disable ICMP echo requests** is better for evasion because modern networks will block or alert on ICMP.

## Related

- [[firewall-and-ids-evasion]]
- [[port-scanning]]
- [[pivoting]]
- [[osint]]

