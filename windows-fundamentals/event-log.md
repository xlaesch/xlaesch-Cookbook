Windows Event Logs record operating system activity, including PowerShell use, event-log deletion, service starts and stops, and RDP activity. Each record has an Event ID that distinguishes the event type.

# Log Types

| **Log** | **Description** |
| --- | --- |
| Application | Events relating to applications on the system. |
| System | Events relating to basic system components. |
| Security | Security events. |

# Event Viewer

Open **Windows + R**, then enter `eventvwr`.

[Windows security event log cheatsheet](https://andreafortuna.org/2019/06/12/windows-security-event-logs-my-own-cheatsheet/).

# Query Events

| **Option** | **Description** |
| --- | --- |
| `query-events` | Query events from a log or log file. |
| `/rd` | Reverse direction. |
| `/count` | Log count. |
| `/format` | Output format. |
| `/q` | XPath query. |

```powershell
# query the most recent Security event with Event ID 4625
wevtutil query-events Security /rd:true /count:1 /format:text /q:"Event[System[(EventID=4625)]]"
```
