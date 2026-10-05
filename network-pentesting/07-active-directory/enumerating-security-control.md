---
tags: [ad, windows, defense-evasion]
aliases: [Enumerating Security Controls]
---

# Windows Defender

```powershell
# check the status of defender
Get-MpComputerStatus
```

# AppLocker

An application whitelist solution to prevent malware and unapproved software to run. It gives administrator control over which applications and files users can run, providing granular control over executables, scripts, Windows installer files, DLLs, packages apps, and packed app installers. 

```powershell
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

# Constrained Language Mode

Locks down many of the features needed to use Powershell effectively, such as blocking COM objects, only allowing. NEt types, etc.

```powershell
$ExecutionContext.SessionState.LanguageMode
```

# LAPS

Local Administrator Password Solution (LAPS) used to randomize and rotate local administrator passwords on Windows hosts and prevent lateral movement. The LAPSToolkit allows us to view the many functions related to LAPS.

```powershell
# show groups delegated to read LAPS passwords
Find-LAPSDelegatedGroups
```

Users with "All Extended Rights" can read LAPS passwords and may be less protected than users in delegated groups.

```powershell
# check for users with All Extended Rights
Find-AdmPwdExtendedRights

# search for computer have LAPS enabled
Get-LAPSComputers
```

## Related

- [[other|Other Privesc]]
- [[05-privilege-escalation/windows/credential-hunting|Credential Hunting]]
- [[user-interaction]]
- [[password-spraying]]

