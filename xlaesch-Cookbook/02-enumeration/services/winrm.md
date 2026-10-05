---
tags: [enum, winrm]
aliases: [WinRM]
---
Windows Remote Management (WinRM) is a Windows integrated remote management protocol based on the CLI. Uses the Simple Object Access Protocol (SOAP) to establish connections. Must be explicitly enabled and started on Windows. WinRM uses ports 5985 and 5986 with the latter using HTTPS.

Windows Remote Shell (WinRS) let us executed arbitrary commands on the remote system and is part of WinRM.

# WinRM Port Scan

Scan both HTTP and HTTPS WinRM ports.

```shell
# Scanning
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

# Evil-WinRM Login

Use this Linux-based tool to interact with WinRM when credentials are available.

```shell
# Linux-based tool to interact with WinRM
evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!
```

## Related

- [[network-services]]
- [[rdp]]
- [[wmi]]
- [[windows-credentials]]

