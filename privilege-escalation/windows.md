---
icon: windows
---

# Windows

## Enumeration

* [WinPeas](https://github.com/peass-ng/PEASS-ng): Windows Privilege Escalation Awesome Scripts
* [PrivescCheck](https://github.com/itm4n/PrivescCheck)
* [WES-NG](https://github.com/bitsadmin/wesng): Windows Exploit Suggester
* Metasploit. If you have a meterpreter shell you can run `multi/recon/local_exploit_suggester`&#x20;

## Abusing Dangerous Privileges

`whoami /priv` allows you to view what privileges your user has.

#### SeBackup / SeRestore

Allows user to read and write to any file in the system, ignore DACL. If enabled we can exfiltrate SAM and SYSTEM hashes and retrieve them with Impacket and perform a Pass-the-Hash (PtH) attack:

```
reg save hklm\system C:\Users\THMBackup\system.hive
reg save hklm\sam C:\Users\THMBackup\sam.hive
```

#### SeTakeOwnership

Allows user to taken ownership of any object on the system. We can take a service or executable that is run with `SYSTEM` privileges and replace the original binary for any payload we like.

{% hint style="info" %}
Being owner of a file does not mean you have privileges but mean you can assign privileges.
{% endhint %}

#### SeImpersonate / SeAssignPrimaryToken

Allows user to run a process that "borrows" the identity and permissions of another user that connects to it. Is necessary for many services. Most of the time can be leveraged with a Potato exploit (check system version).

## Service Misconfigurations

Windows services are managed by Service Control Manager (SCM). Manages state of service and current status. Each service has an associated executable run by SCM with special function ran by SCM.

`sc qc SERVICE` to view the structure of a service. `binary_path_name` can allow us to access the Discretionary Access Control List (DACL) which indicates who has permissions start, stop, and pause a service.

`reg query HKLM\SYSTEM\CurrentControlSet\Services\` to view service configurations.

If a service has weak permissions that allows an attacker to modify or replace the service. An attacker can overwrite the legitimate file with a malicious one to gain those privileges. Involves restarting the service.

#### Insecure Service Permissions

If the DACL service (not the executable DACL) is modifiable, you can point to any executable with any level of permissions (even `SYSTEM`).

#### Unquoted Service Permissions

If a service is configured to an unquoted executable and spaces exists, the CMD does not know how to properly parse the path because a space defines a new argument. Instead of failing it will try every possibility with or without spaces. We can leverage this by placing a payload at one of the earlier paths it tries.

{% hint style="info" %}
This is not easily done because most services are stored in the `Program Files` directory. Which is unwritable for unprivileged users.
{% endhint %}

## Quick Wins (More common in CTF)

#### Vulnerable Software

`wimc product get name,version,vendor` shows software installed on machine (Note: may not show all programs, as some are installed differently)

#### Unattended Windows Installations

Administrators need to install Windows on a large number of hosts so they use Windows Deployment Services to deploy a single OS image to a lot of hosts through the network. Requires an Administrator account to perform initial setup, which may be stored on local machine:

```
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
```

#### Powershell History

`type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`&#x20;

#### Saved Windows Credentials

`cmdkey /list` lists all credentials stored in the Windows Credential Manager (Note: won't allow you to view them directly)

`runas /savecred /user:admin cmd.exe` run a cmd.exe as the user stored in the Credential Manager

#### Internet Information Services (IIS) Configuration

Is responsible for the default web server on Windows installs. Will sometimes store password for databases or configurated authentications.

```
C:\inetpub\wwwroot\web.config
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

#### Retrieve Credentials from software

PuTTY (SSH client on windows): stores connection parameters such as IPs and SSH passwords. Only accessible with Proxy  `reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s`

#### AlwaysInstallElevated

Windows Installer Files (or .msi) are used to install applications on system. They are usually run with the privilege level of the user that starts it. They can be configured to run with higher privilege accounts.

```
#Two registry values have to be set
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

Make a malicious .msi file to exploit.

#### Scheduled Tasks

`schtasks` to view scheduled tasks. We can view `Task to Run` and `Run as User` , changing either might get a payload to be run by a user with higher privileges.

#### Service Configurations

`reg query HKLM\SYSTEM\CurrentControlSet\Services\` to view service configurations to learn about a service.&#x20;

