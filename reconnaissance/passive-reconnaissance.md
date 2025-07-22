---
description: >-
  Gather information about target systems using publicly available knowledge
  without directly engaging with the target.
---

# Passive Reconnaissance

`whois` is a request and response protocol on TCP port 43. Details:

* Registrar: under which company is the sale of the internet domain under.
* Contact info of registrant.
* Creation, update, and expiration dates.
* Name Server.

`nslookup OPTIONS DOMAIN_NAME SERVER` to perform name server lookup.

* OPTIONS: `A` for IPv4 and `AAAA` for IPv6
* SERVER: the DNS server that you want to query

`dig @SERVER DOMAIN_NAME TYPE`  is a domain information group, more advanced than nslookup.

[DNS Dumpster](https://dnsdumpster.com/): collects DNS queries and sometimes provides subdomains

[Shodan.io](https://www.shodan.io/): attempts to connect to every device reachable online and builds a search engine of the results.
