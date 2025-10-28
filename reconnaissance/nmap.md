---
description: >-
  Stands for network mapper, allows to find out more information about a group
  of hosts or subnet.
---
# Theory


---

# Practice

`nmap SCAN_TYPE OPTIONS TARGET` is the general syntax&#x20;

### Live Host Discovery

`-sn` option to perform live host discovery

`-sL` option to show the list of hosts Nmap will scan

#### Scan Types:

`-PR` ARP Scan

`-PE` ICMP echo Scan (Note: most firewalls block ICMP echo, use `-PP` ICMP Timestamp or `-PM` ICMP address mask scan if blocked)

`-PS` TCP SYN ping. Specify ports like so `-PS21-25` , ports 21 through 25, if not port is specificed scans common ports

`-PA` TCP ACK ping. Must be privileged.

`-PU` TCP UDP ping. (Note: UDP packets to open ports don't reply, but closed port reply with ICMP port unreachable packet).

#### Reverse-DNS Lookup:

`-n` To skip reverse DNS lookup.

&#x20;`-R` To query the DNS server for offline hosts.

`-dns-servers DNS_SERVER` to specify DNS

### Port Scans

`-F` fast mode option, decrease number of scanned ports to 100 common ports.

`-r` scan ports in consecutive order instead of random

`-p` to specify which ports. Can use list format `-p22,80,443` or range (inclusive) `-p1-1023` . `-p-` for all ports

`-T<0-5>` to specify scan timing. 0 is slowest and 5 is fastest. timing is to avoid IDS alerts.

`--min-rate` and `--max-rate` to specify packets per second

`--min-parallelism` and `--max-parallelism` to specify how many probes (threads that send packets) run at once.

#### IDS and Firewall Bypasses

`-f` divides IP data into 8 bytes or less `-ff` splits data into 16 byte fragments and so on.

{% hint style="info" %}
Divides the portion of the IP header, to be reassembled later on using the identification and fragment offset headers in the IP header.&#x20;
{% endhint %}

#### Spoofing

`-S SPOOFED_IP` to spoof IP (Note: the response will route to the spoofed ip)

`--spoof-mac` to spoof mac address. Only possible if attack and target machine are on the same ethernet or Wi-Fi.

{% hint style="info" %}
Spoofing only works under very specific conditions. Using decoys makes it harder for the attacker to be found.
{% endhint %}

`-D` to specify decoy IPs. e.g. `nmap -D 10.10.0.01,10.10.0.2,ME TARGET_IP` , `ME` is your IP address. You can also use `RND` to assign random IP addresses.

#### Scan Types

`-sT` TCP Connect Scan. Answers with SYN/ACK if open

`-sS` TCP SYN Scan. Must be privileged. Does not complete 3-way handshake, tears down connection after response to reduce change of being logged.

`-sU` UDP Scan.

`-sN` TCP Null Scan. No TCP flag. No response is received if TCP port is open.

`-sF` TCP FIN Scan. No response is received if TCP port is open.

`-sX` Xmas Scan. Sets FIN, PSH, URG flags all at once. RST ACK if closed. Useful against stateless firewalls that deny SYN flags.

`-sA` TCP ACK. Targets responds with RST regardless of port. useful to determine firewall rules.\\

`-sI ZOMBIE_IP TARGET_IP` Idle/Zombie scan. Makes probes appear to come from the ZOMBIE\_IP.&#x20;

#### Post-Scan Analysis Options

`-sV` service and version information detection.

`-O` can determine OSes based on behaviors.

`--script "SCRIPT_NAME"` to specify built-in scripts

### Additional Info

Nmap classifies results as the following:

1. open: service listening on port
2. closed: no service is listening on port but port is accessible (⇒ not blocked by firewall)
3. filtered: cannot determine if port is open or closed (⇒firewall)
4. unfiltered: cannot determine if port is open or closed although port is accessible
5. Open|Filtered: cannot determine if open or filtered
6. Closed|Filtered: cannot determined if closed or filtered

Nmap can only manipulates certain parts (**in red**) of the TCP header

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

To understand the different TCP flags, view the Principles of Networks page.

{% content-ref url="../principles-of-networks.md" %}
[principles-of-networks.md](../principles-of-networks.md)
{% endcontent-ref %}

