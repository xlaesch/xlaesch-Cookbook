Event Logs are logs collected through the Windows operating system. Some important events can be (powershell, deleting event logs, starting and stopping services, RDP activity, etc.)

Each record type has an "Event ID" value to distinguish it from each other. There are 3 main event log titles
- Applications - anything relating to applications in the system.
- System - relating to basic components
- Security

To open the GUI we do "Windows + R" and `eventvwr`

An event list can be found: https://andreafortuna.org/2019/06/12/windows-security-event-logs-my-own-cheatsheet/

```
wevtutil query-events Security /rd:true /count:1 /format:text /q:"Event[System[(EventID=4625)]]"
```

"query-events" parameter : Query events from a log or log file.
"/rd" parameter : Reverse direction.
"/count" parameter : Log count.
"/format" parameter : Output format.
"/q" parameter : XPathQuery.

