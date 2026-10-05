---
tags: [ad, windows, kerberos]
aliases: [Double Hop]
---
Kerberos tickets are signed pieces of data from the KDC that state what resources an account can access.

Often in winRM sessions, when an attacker attempts to use Kerberos authentication across two or more hops they might be denied authentication despite having access. The initial authentication is usually performed over SMB or LDAP, which means the user's NTLM hash would be stored in memory. Thus, when we try to authenticate over two or more connections, the user's password is never cached as part of their login. When we use Kerberos to establish a remote session, we are not using a password for authentication.

```powershell
# verify that the remote host doesn't have user credentials in memory.
Enter-PSSession -ComputerName DEV01 -Credential INLANEFREIGHT\backupadm

.\mimikatz "privilege::debug" "sekurlsa::logonpasswords" exit
# the backupadm credential does not exist.
```

When we try to issue a multi-server command, our credentials will not be sent from the first machine to the second. The TGT ticket is not sent to the remote session, so the user has no way to prove their identity, and commands will no longer be run in this user's context. When authenticating to the target host, the user's ticket-granting service (TGS) ticket is sent to the remote service, which allows command execution, but the user's TGT ticket is not sent. 

In an unconstrained delegation host, the double hop problem doe snot exist.

> If connected via RDP, this is not an issue since our password is stored in memory.

# PSCredential Object Workaround

We can connect to the remote host via Host A and set up a PSCredential object to pass our credentials agian. 

```powershell
# check the cached Kerberos tickets
klist

# create the object
$SecPassword = ConvertTo-SecureString '!qazXSW@' -AsPlainText -Forcek
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\backupadm', $SecPassword)

# query with powerview using the credential
get-domainuser -spn -credential $Cred | select samaccountname
```

# Register PSSession Configuration Workaround

If we are on a domain-joined host and connect remotely to another using WinRM, we can interact directly with the DC or other hosts without having to create credential object.

```powershell
# create the winRM session on the target host
Enter-PSSession -ComputerName ACADEMY-AEN-DEV01.INLANEFREIGHT.LOCAL -Credential inlanefreight\backupadm

# verify that the cached credentials don't exist
klist

# register a new session configuration
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm

# then restart the winRM service so it can take effect
Restart-Service WinRM

# then enter back into the session
``` 

>Note: We cannot use `Register-PSSessionConfiguration` from an evil-winrm shell because we won't be able to get the credentials popup. Furthermore, if we try to run this by first setting up a PSCredential object and then attempting to run the command by passing credentials like `-RunAsCredential $Cred`, we will get an error because we can only use `RunAs` from an elevated PowerShell terminal. Therefore, this method will not work via an evil-winrm session as it requires GUI access and a proper PowerShell console. Furthermore, in our testing, we could not get this method to work from PowerShell on a Parrot or Ubuntu attack host due to certain limitations on how PowerShell on Linux works with Kerberos credentials. This method is still highly effective if we are testing from a Windows attack host and have a set of credentials or compromise a host and can connect via RDP to use it as a "jump host" to mount further attacks against hosts in the environment.

## Related

- [[lateral-movement-and-pivoting]]
- [[kerberoasting]]
- [[enumeration]]
- [[password-spraying]]

