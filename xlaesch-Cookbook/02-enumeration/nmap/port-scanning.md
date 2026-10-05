---
tags: [enum, linux, nmap]
aliases: [Port Scanning]
---
After we have found that our target is alive, we want to get a more accurate picture of the system: open ports and its services, service versions, information that the services provided, and operating system.

Ports can have 6 different states:

| State              | Description                                                                                                                                                                                           |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `open`             | This indicates that the connection to the scanned port has been established. These connections can be TCP connections, UDP datagrams as well as SCTP associations.                                    |
| `closed`           | When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an RST flag. This scanning method can also be used to determine if our target is alive or not. |
| `filtered`         | Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.                |
| `unfiltered`       | This state of a port only occurs during the TCP-ACK scan and means that the port is accessible, but it cannot be determined whether it is open or closed.                                             |
| `open\|filtered`   | If we do not get a response for a specific port, Nmap will set it to that state. This indicates that a firewall or packet filter may protect the port.                                                |
| `closed\|filtered` | This state only occurs in the IP ID idle scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.                                             |

# SYN Scan

**SYN Scan** is the `nmap` root default.

```shell
# SYN scan (-sS) scanning top 10 TCP ports
sudo nmap $HOST --top-ports=10 
```

# Single-Port Trace

Use packet trace with DNS resolution disabled (`-n`), ICMP echo requests disabled (`-Pn`), and ARP ping disabled when you need to see exactly what the target returns.

```shell
# scan at port with DNS resolution (-n), packet trace and disabled ICMP echo requests (-Pn) and ARP ping
sudo nmap $HOST -p $PORT --packet-trace -Pn -n --disable-arp-ping
```

# TCP Connect Scan

**TCP Connect Scan** uses the TCP three-way handshake to determine if a specific port on a target host is open or closed. It sends a SYN packet and waits for a response. This scan is highly accurate but not stealthy because it fully establishes a connection, creating logs on most systems.

```shell
# TCP connect scan (-sT) with displaying reason for result and reducing max retries to 0 (from default of 5)
sudo nmap $HOST -p $PORT --reason -sT --max-retries=0
```

# Version Detection

Use `-sV` after finding open ports to identify service versions.

```shell
# version scan (-sV)
sudo nmap $HOST -Pn -p $PORT -sV
```

# Default Scripts and Version Detection

Use this when moving from port discovery into service enumeration across all ports.

```shell
# run default NSE scripts and version scan on all ports
sudo nmap $HOST -p- -sV -sC
```

# Aggressive Scan

Aggressive scan combines service detection, traceroute, and default scripts.

```shell
# aggressive scan (-A) with service detection, traceroute and default scripts
sudo nmap $HOST -p $PORT -A
```

# Vulnerability Scripts

Use `--script vuln` for an initial vulnerability assessment against a known service port.

```shell
# vulnerability assessment (--script vuln) on HTTP port 80
sudo nmap $HOST -p 80 -sV --script vuln 
```

# Fast Network Scan

Use this when you need quick coverage of the top 100 ports across a subnet.

```shell
# insance scan (-T 5) scanning top 100 ports (-F) and outputing in normal formats (-oN)
sudo nmap $HOST/24 -F -oN tnet -T 5
```

# ACK Scan

**ACK Scan** much harder to filter for firewalls and IDS/IPS systems because they only send the `ACK` flag, firewalls cannot determine whether the connection was first established from external network or the internal network.

```shell
# ACK scan on specified ports
sudo nmap $HOST -p 21,22,25 -sA
```

# UDP Scan

**UDP scan** does not require a three-way handshake because it is a stateless protocol, leading to longer timeouts. System administrators will often forget to filter the UDP ports.

```shell
# UDP scan
sudo nmap $HOST -sU
```

## Related

- [[host-discovery]]
- [[firewall-and-ids-evasion]]
- [[web]]
- [[nfs]]

