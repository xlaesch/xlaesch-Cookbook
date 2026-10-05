---
tags: [ad, linux, ad-recon]
aliases: [AD Reconnaissance]
---

Active Directory (AD) is a directory service for Windows enterprise environments. Based on the x.500 and LDAP. 

The key data points we should be looking for when enumerating a domain.

|**Data Point**|**Description**|
|---|---|
|`AD Users`|We are trying to enumerate valid user accounts we can target for password spraying.|
|`AD Joined Computers`|Key Computers include Domain Controllers, file servers, SQL servers, web servers, Exchange mail servers, database servers, etc.|
|`Key Services`|Kerberos, NetBIOS, LDAP, DNS|
|`Vulnerable Hosts and Services`|Anything that can be a quick win. ( a.k.a an easy host to exploit and gain a foothold)|
# Network

If we are in-network we can listen in on the network via Wireshark and TCPDump.  Especially necessary if we are in a black box assessment.
`
```shell
# start wireshark attack
sudo -E wireshark

# or
sudo tcpdump -i ens224
```

We'll usually look for ARP requests and replies which will indicate which hosts exist on the system. Then, we can use MDNS to find the hostnames of certain IPs.

Similarly, we can use Responder in passive analysis mode to see requests on the network.

```shell
sudo responder I ens224 -A
```

We can also check ICMP requests and replies to reach out and interact with a host. We can issue multiple hosts. 

```shell
fping -asgq 172.16.5.0/23
```

We can also use nmap to identify the naming standard used by NetBIOS and DNS, and especially identify where the Domain Controller exists.


# Users

If a client does not provide a user to start testing with, we will need to establish a foothold onto the system. We can therefore perofrm an internal AD Username Enumeration with the user lists from [Insidetrust](https://github.com/insidetrust/statistically-likely-usernames). 
```shell
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users
```

## Related

- [[password-spraying]]
- [[kerberoasting]]
- [[credential-enumeration]]
- [[xlaesch-Cookbook/07-active-directory/old/bloodhound]]

