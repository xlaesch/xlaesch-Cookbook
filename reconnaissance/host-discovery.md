---
description: hello world!
---

# Theory
To conduct a penetration test we have to get an overview of which systems are online, and which ones we can work with.

Some options during scanning can have advantages:
* **Disable DNS Resolution** saves time by skipping reverse DNS lookups
* **Disable ARP Ping** is better because ARP is local-only, if you're scanning across routers ARP won't work.
* **Disable ICMP echo requests** is better for evasion because modern networks will block or alert on ICMP.

---

# Practice
```shell
# scan a target network range without scanning ports (-sn) and storing the result in all formats starting with the name 'tnet'
sudo nmap $HOST/24 -sn -oA tnet | grep for | cut -d" " -f5

# perform host discovery on a predefined list
sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

# check whether a single IP is alive using ICMP echo requests (-PE) and packet tracing for more verbose output
sudo nmap $HOST -sn -oA host -PE --packet-trace 

# check whether a single IP is alive without using ARP pings
sudo nmap $HOST -sn -oA host -PE --packet-trace --disable-arp-ping 
```