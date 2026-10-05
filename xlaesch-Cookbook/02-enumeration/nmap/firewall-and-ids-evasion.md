---
tags: [enum, linux, nmap]
aliases: [Firewall and IDS Evasion]
---
When a port is shown as `filtered` , packets can be `dropped` where they are just ignored, and no response is returned from the host. They can also be `rejected`, where TCP packets are returned with an RST flag.

The detection of IDS/IPS systems is difficult because these are passive traffic monitoring systems. We should use several virtual private servers (VPS) to determine the existence of such systems, because if the administrator detects a potential attack, the first step is to block the IP address from which the potential attack comes. If we scan from a single host (VPS) and at any time that host is blocked and has no access to the target network, we know that the administrator has taken some security measures.

# Decoy SYN Scan

With **Decoys**, we can bypass blocked specific subnets. `nmap` generates random IP address inserted into the IP header to disguise the origin of the packet sent. Spoofed packets will often be filtered out by ISPs and routers.

```shell
# SYN scan using 5 random IP address decoys (-D RND:5)
sudo nmap $HOST -p $PORT -sS -D RND:5
```

# Source IP Spoofing

Use this when testing how filtering reacts to a different source address through a specific interface.

```shell
# scan using different source IP address (-S IP) through a specified interface
sudo nmap $HOST -p $PORT -S 10.129.2.200 -e tun0
```

# Trusted Source Port

**DNS Proxying** can be helpful because the company's DNS servers are usually more trusted than those from the Internet. We could use those servers to interact hosts of the internal network. We can also do this by changing the source port of our scans, because some firewalls will permit traffic that appears from trusted ports so our TCP packet will be trusted and passed through. Only useful if we are within the DMZ.

```shell
# SYN scan from a DNS port (53) (using --source-port)
sudo nmap $PORT -p $PORT -sS --source-port 53
```

## Related

- [[host-discovery]]
- [[port-scanning]]
- [[llmnr nbt-ns poisoning|LLMNR/NBT-NS Poisoning]]
- [[pivoting]]

