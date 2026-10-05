The Windows Registry stores operating system and program configuration. Attackers can enumerate the system through it and add entries for persistence.

# Keys and Values

Registry entries are located in `%SystemRoot%\System32\Config`. Keys act as containers, similar to folders, and hold values.

> `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows` refers to the `Windows` subkey within `Software\Microsoft` under `HKEY_LOCAL_MACHINE`.

# Root Keys

| **Key** | **Description** |
| --- | --- |
| `HKEY_LOCAL_MACHINE` (`HKLM`) | Contains `HARDWARE`, `SAM`, `SECURITY`, `SOFTWARE`, and `SYSTEM`. |
| `HKEY_CURRENT_CONFIG` (`HKCC`) | Hardware configuration. |
| `HKEY_CLASSES_ROOT` (`HKCR`) | Software settings, shortcuts, and UI configuration. |
| `HKEY_CURRENT_USER` (`HKCU`) | Configuration for the logged-in user. |
| `HKEY_USERS` (`HKU`) | Configuration for all users. |

## HKLM Subkeys

| **Subkey** | **Description** |
| --- | --- |
| `HARDWARE` | Hardware. |
| `SAM` | Encrypted versions of passwords. |
| `SECURITY` | Security policies. |
| `SOFTWARE` | Operating system service and program configuration. |
| `SYSTEM` | System. |

# Registry Editor

Open **Windows + R**, then enter `regedit`. Exported registry files use the `.reg` extension.

# Query a Key

```powershell
# query a registry key from the command line
reg query "HKEY_LOCAL_MACHINE\SYSTEM\<subkey>"
```
