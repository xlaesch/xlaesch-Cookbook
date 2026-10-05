Windows Management Instrumentation (WMI) provides access to Windows operating system components, allowing local and remote access.

# Operating System Information

```powershell
# information about the operating system
wmic os list brief
```

# User Accounts

```powershell
# get the names of users on the system
wmic useraccount get name
```
