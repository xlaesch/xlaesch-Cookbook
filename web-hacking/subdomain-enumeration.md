# Subdomain Enumeration

### Brute Force

DNS Brute Force by enumerating different subdomains from a list and querying the DNS.

`dnsrecon` , `Sublist3r`

### Virtual Host

Different websites that are hosted on one single server. So for a given web server IP address we might have numerous websites hosted. By manually setting the `Host` header of every outgoing request to the web server we can enumerate the subdomains.

`ffuf -w /path/to/your/wordlist.txt -u http://10.10.11.150 -H "Host: FUZZ.example-corp.com" -fc 404,403`

* `-w`: Specifies the wordlist file.
* `-u`: The target URL (the server's IP).
* `-H`: Sets a custom header. We are modifying the `Host` header.
* `-fc`: Filter (hide) responses with these HTTP status codes. We'll hide common "Not Found" codes like `404` and `403`.

### OSINT

Viewing the Certificate Transparency Logs, a service intended to stop malicious/accident certificates from being used, may reveal some subdomains. Every SSL/TLS certificate created for a domain name is stored there.

Google Dorking can also reveal subdomains. `site: filter` works with wildcard `*`&#x20;
