---
tags: [enum, linux, rdp]
aliases: [RDP]
---
A protocol developed by Microsoft for remote access to a computer running Windows. Allows display and control commands to be transmitted via the GUI encrypted over IP networks. Usually uses port 3389. Usually handles via TLS, so traffic and login process is encrypted.

The `Remote Desktop` service is installed by default on Windows Servers.

RDP cookies can be identified by threat hunters and EDR to lock attackers out.

# RDP Script Scan

Use Nmap scripts to identify exposed RDP and collect protocol details.

```shell
# Scnaning
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```

# Security Check

Use `rdp-sec-check` to review RDP security settings.

```shell
# security check
sudo cpan
./rdp-sec-check.pl 10.129.201.248
```

# Start RDP Session

Use this once credentials are available.

```shell
# Initiate RDP session
xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
```

## Related

- [[rdp-attacks]]
- [[network-services]]
- [[r-services|R-Services]]
- [[winrm]]

