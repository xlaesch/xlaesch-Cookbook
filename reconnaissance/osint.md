---
description: >-
  Gather information about target systems using publicly available knowledge
  without directly engaging with the target.
---
# Theory

## Domain Information

When passively gathering information, we can use third-party services to understand our target better. The main website is oftentimes what should be scrutinized first. Inspecting the SSL certificate for the target domain can expose sibling subdomains that share the same certificate, and cross-referencing Certificate Transparency logs on [crt.sh](https://crt.sh/) helps surface newly issued certificates for related hosts. Tools such as [Shodan](https://www.shodan.io/) enumerate Internet-facing systems by scanning for open TCP/IP ports, letting us map exposed services quickly. 

Reviewing DNS records complements these efforts by enumerating hostnames and revealing how the organization structures its public presence. Several records exist
* `A` records: the IP addresses that point to specific (sub)domain.
* `MX` records: The mail server records show us which mail server is responsible for managing the email for the company.
* `NS` records: show which name servers are used to resolve the FQDN to IP addresses =
* `TXT` records: this type of record often contains verification keys for different 3rd party providers or other security features.

### Cloud Resources

Cloud resources can be vulnerable if configured improperly. Often cloud storage is added to the DNS list when used for administrative purposes (for easier access by employees). 

## Staff

Discovering employees on social media platforms can reveal a lot about what technologies are being used based on their skillset.

---
# Practice

## Domain Information

```shell
# output results in json format
curl -s https://crt.sh/\?q\=$DOMAIN\&output\=json | jq .

# filter by subdomain
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u

# from that subdomain list we can generate a list of IP addresses
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

# from an IP list query Shodan for more info
for i in $(cat ip-addresses.txt);do shodan host $i;done

# 
dig any $DOMAIN
```

## Cloud Resources

* Google Dorking is effective for this. Using search parameters such as `inurl:` and `intext:` can reveal files that are publicly accessible.
* [domain.glass](https://domain.glass/) can also tell us about the company's infrastructure.
* [GrayHatWarfare](https://buckets.grayhatwarfare.com/)is can also passively find files on a given cloud storage.