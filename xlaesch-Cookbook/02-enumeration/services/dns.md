---
tags: [enum, linux, dns]
aliases: [DNS]
---
Domain Name System (DNS) is a system for resolving computer names into IP addresses. A zone is a domain and all its subdomains that are managed together by the same DNS servers. There is no central database, so it is organized as follows:

| **Server Type**                | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DNS Root Server`              | The root servers of the DNS are responsible for the top-level domains (`TLD`). As the last instance, they are only requested if the name server does not respond. Thus, a root server is a central interface between users and content on the Internet, as it links domain and IP address. The [Internet Corporation for Assigned Names and Numbers](https://www.icann.org/) (`ICANN`) coordinates the work of the root name servers. There are `13` such root servers around the globe. |
| `Authoritative Nameserver`     | Authoritative name servers hold authority for a particular zone. They only answer queries from their area of responsibility, and their information is binding. If an authoritative name server cannot answer a client's query, the root name server takes over at that point. Based on the country, company, etc., authoritative nameservers provide answers to recursive DNS nameservers, assisting in finding the specific web server(s).                                              |
| `Non-authoritative Nameserver` | Non-authoritative name servers are not responsible for a particular DNS zone. Instead, they collect information on specific DNS zones themselves, which is done using recursive or iterative DNS querying.                                                                                                                                                                                                                                                                               |
| `Caching DNS Server`           | Caching DNS servers cache information from other name servers for a specified period. The authoritative name server determines the duration of this storage.                                                                                                                                                                                                                                                                                                                             |
| `Forwarding Server`            | Forwarding servers perform only one function: they forward DNS queries to another DNS server.                                                                                                                                                                                                                                                                                                                                                                                            |
| `Resolver`                     | Resolvers are not authoritative DNS servers but perform name resolution locally in the computer or router.                                                                                                                                                                                                                                                                                                                                                                               |

DNS also stores and outputs other information such as which computer servers as the e-mail server for the domain in question. There exists different DNS records.

| **DNS Record** | **Description**                                                                                                                                                                                                                                   |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `A`            | Returns an IPv4 address of the requested domain as a result.                                                                                                                                                                                      |
| `AAAA`         | Returns an IPv6 address of the requested domain.                                                                                                                                                                                                  |
| `MX`           | Returns the responsible mail servers as a result.                                                                                                                                                                                                 |
| `NS`           | Returns the DNS servers (nameservers) of the domain.                                                                                                                                                                                              |
| `TXT`          | This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam. |
| `CNAME`        | This record serves as an alias for another domain name. If you want the domain www.hackthebox.eu to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for www.hackthebox.eu.               |
| `PTR`          | The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.                                                                                                                                     |
| `SOA`          | Provides information about the corresponding DNS zone and email address of the administrative contact.                                                                                                                                            |

# Nameserver Lookup

Start by identifying the authoritative nameservers for the domain.

```shell
# query the DNS server to show which name servers are known
dig ns $DOMAIN @$HOST
```

# DNS Version Query

Use a class CHAOS TXT query when testing for exposed BIND version information.

```shell
# query DNS server's version using a class CAHOS query and type TXT
dig CH TXT version.bind $HOST
```

# Record Review

Use `any` when you want a broad look at records the DNS server will return for a given domain.

```shell
# view all available records
dig any $DOMAIN @$HOST
```

# Zone Transfer

All DNS servers work with three configuration files:
* local DNS configuration files
* zone files is a text file that describes a DNS zone with the BIND file format (a point of delegation in the DNS tree)
* reverse name resolution files helps resolve a Fully Qualified Domain Name (FQDN) from the IP address using PTR records.

Zone transfers refers to the transfer of zones to another server in DNS (over TCP port 53). This is to prevent a DNS failure from affecting usage. Each name server on their respective zone has to update their zone file which is done via Asynchronous Full Transfer Zone (AXFR) using a secret `rndc-key`.

The original data of the name server is located on the **primary** name server, to increase reliability additional name serves called **secondary** are implemented for each zone. All DNS entries are only modified on the primary.

```shell
# query DNS server AXFR zone transfer
dig axfr $DOMAIN @$HOST
```

If a DNS server allows unauthorized clients to request the entire zone, we can enumerate other domains in the network.
# Subdomain Brute Force

Subdomains are extensions of the main domain, created to organize and separate different functionalities of the a website. Subdomains allow for development and staging environments, hidden login credentials, legacy applications, and sensitive information. They can be enumerated actively via DNS zone transfers and brute-force enumeration or passively via Certificate Transparency (CT) logs and search engines.

```shell
# brute force subdomains of DNS server
dnsenum --dnsserver $HOST --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/wordlists/seclists/Discovery/DNS/fierce-hostlist.txt $DOMAIN

# brute force subdomains of a domain
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
```

These commands mainly query the DNS server to check if records exist among other things (zone transfer, Google scraping, etc.)

# Virtual Hosting

Virtual Hosting is the ability to of web server to distinguish between multiple websites sharing the same IP address. Achieved via the `HTTP Host` header in every HTTP request. Name-Based Virtual Hosting relies only on the HTTP Host Header. IP-Based Virtual Hosting assigns a unique IP address to each website hosted on the server. Port-Based Virtual Hosting associate different websites to different ports.

```shell
# identify potential virtual hosts 
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

# Certificate Transparency Logs

CT logs are public, append-only ledges that record the issuance of SSL/TLS certificates. They allow for early detection of rogue certificates, accountability for certificate authorities, and strengthens the Web's PKI.

```shell
# use CT logs to identify subdomains
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
```

## Related

- [[dns-attacks]]
- [[subdomain-discovery]]
- [[llmnr nbt-ns poisoning|LLMNR/NBT-NS Poisoning]]
- [[osint]]

