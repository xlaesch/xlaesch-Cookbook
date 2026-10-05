---
tags: [privesc, windows, scheduled-tasks]
aliases: [Scheduled Tasks]
---
# Scheduled Tasks

We can use the schtasks command to enumerate scheduled tasks on the system

```powershell
schtasks /query /fo LIST /v
# or in powershell
Get-ScheduledTask | select TaskName,State
```

By default we can only see tasks created by our user and default scheduled tasks. Scheduled tasks created by admins are stored in `C:\Windows\System32\Tasks` which standard users do not have access to. A scheduled tasks that runs as an administrator may be configured with weak file/folder permissions. 

```powershell
.\accesschk64.exe /accepteula -s -d C:\Scripts\
```

## Related

- [[user-interaction]]
- [[other|Other Privesc]]
- [[windows-old]]
- [[enumerating-security-control|Enumerating Security Controls]]

