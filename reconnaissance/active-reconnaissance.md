---
description: Gather information about target systems by making contact with the target.
---

# Active Reconnaissance

### Methods

**Web Browsers:**

* Extensions: FoxyProxy (change proxy server that is being used to access target website), User-Agent Switcher and Manager, Wappalyzer (view technologies being used on websites)

`fping` similar to `ping` in that it uses ICMP requests, but we can specify multiple targets, so its more versatile.

* `-a` shows systems that are alive
* `-g` generates a target list from a supplied IP netmask
* `-q` quiet mode, doesn’t show per-probe results or ICMP error mesasages

`ping` sends an ICMP echo packet to a remote system, an online system will back ICMP echo reply.

`traceroute` traces routes taken by the packets from your system to another host (number of routers between the two systems)

* enumerates the network with ICMP packets, incrementing their TTL until the route is mapped

{% content-ref url="nmap.md" %}
[nmap.md](nmap.md)
{% endcontent-ref %}

