Windows Registry is a database that contains the OSes configuration for programs. The registry is usually a target for attackers, they can view and enumerate much of the system through it and even add their own entries for persistence.

Windows registry entries are located in `%SystemRoot%\System32\Config`. Registry contains keys and values with keys being similar to container objects like folders. 

> HKEY_LOCAL_MACHINE\Software\Microsoft\Windows refers to the subkey "Windows" of the subkey "Microsoft" of the subkey "Software" of the HKEY_LOCAL_MACHINE root key.

`HKEY_LOCAL_MACHINE` or `HKLM` contains
- HARDWARE
- SAM - containing encrypted versions of the passwords.
- SECURITY - containing security policies
- SOFTWARE - configurations of the OS services as well as the programs
- SYSTEM
HKEY_CURRENT_CONFIG or HKCC contains hardware configuration.
HKEY_CLASSES_ROOT or HKCR contains software settings, shortcuts, UI, etc.
HKEY_CURRENT_USER or HKCU contains configuration of logged-in users.
HKEY_USERS or HKU contains all users configuration. 

Reg extension files is the file format saved when exporting the registry files. 

To access it we can use "Windows + R" then `regedit`.

It is also possible to execute operations via the command line.

```
reg query HKEY_LOCAL_MACHINE\SYSTEM\...
```