---
tags: [enum, windows, wmi, impacket]
aliases: [WMI]
---
Windows Management Instrumentation (WMI) allows read and write access to almost all settings on Windows systems. WMI is typically accessed via Powershell, VBScript, or Windows Management Instrumentation Console (WMIC). Is not a single program but is a suite of repositories. Initial communication takes place on TCP port 135.

# WMI Command Execution

Use Impacket `wmiexec.py` to confirm authenticated WMI access and run a command.

```shell
# Connection to WMI
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```

## Related

- [[ipmi]]
- [[winrm]]
- [[user-interaction]]
- [[rdp]]

