A process is a program under execution. Processes have parent-child relationships, with a parent creating one or more child processes.

# Process Relationships

| **Type** | **Description** |
| --- | --- |
| Process | A program under execution. |
| Parent process | A process that created one or more child processes. |
| Child process | A process created by another process. |

# Legitimate Processes

These processes exist in `C:\Windows\System32`.

| **Process** | **Description** |
| --- | --- |
| `wininit.exe` | Windows Initialization Process; starts `services.exe`, `lsass.exe`, and `lsm.exe` with `SYSTEM` privileges. |
| `services.exe` | Starts and stops services. Runs as `SYSTEM`; only one should exist. Child processes include `svchost.exe`, `dllhost.exe`, `taskhost.exe`, and `spoolsv.exe`. |
| `svchost.exe` | Hosts services that run from non-executable DLLs. Responsible for managing multiple DLL services; all DLLs share the same process. |
| `lsass.exe` | Local Security Authority Subsystem Service; handles authentication and contains user passwords. |
| `winlogon.exe` | Performs user login and logout operations. |
| `explorer.exe` | Parent of every GUI process; runs with the logged-in user's privileges. |

# List Processes

```powershell
# list running processes
tasklist
```

# Terminate a Process

```powershell
# terminate a process by its process ID
taskkill /PID <PID>
```
