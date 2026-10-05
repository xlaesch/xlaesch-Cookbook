
Process is a program under execution in an active program. 

Parent-child relationship between the two processes:
- Process - program under execution
- Parent Process - process that created one or more child processes.
- Child Process - created by another process. 

# Legitimate Processes

These all exist on `C:\Windows\System32`

## wininit.exe

Windows Initialization Process responsible for started the `services.exe`, `lsass.exe` and `lsm.exe`. It has `SYSTEM` privileges.

## services.exe

The process responsible for starting and stopping services.  “Svchost.exe”, “dllhost.exe”, “taskhost.exe”, and “spoolsv.exe” are child processes of the “Services.exe”. Ran as `SYSTEM`. Only 1 should exist.

## svchost.exe

A generic host process for services that run from DLLs. DLLs are non-executable so they are run with svchost for triggering the services of the OS . Responsible  for the usage and management of multi-dll services. All DLLs share the same svchost process.

## lsass.exe

Local Security Authority Subsystem Service is responsible for authentication. Contains the user passwords in the system. 

## winlogon.exe

Performs the login and logout operations of the users in the OS. 

## explorer.exe

Parent process of every process that has GUI. Runs with the privileges of logged-in user.

### CLI

```
tasklist
```

Lists running processes.

```
taskkill /PID 2812
```

Terminate processes, necessitates the Process ID.
