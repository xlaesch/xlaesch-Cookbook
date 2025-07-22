---
description: >-
  Open-source tool used to analyze Active Directory environments by revealing
  hidden relationships and identifying potential attack paths.
---

# BloodHound

Operates on a two stage attack model

1. Enumeration: deploy a data collection (Sharphound, `bloodhound-python`) to gather information about AD structure
2. Target Attack: load collected offline data to identify efficient paths to find the goal.

Methodolohy:

1. Run data collector
2. Upload data in administration section
3. Query the explore page to view relationships, specifically the **Pathfindin**g section. if a path exists, Bloodhound will map it out

## Data Collectors

Tool that collects group memberships, session data, access control lists (ACLs), domain trusts, and privileged relationships. May trigger security alerts so to minimize detection we should

* `--ExcludeDCs` by limiting interactions with sensitive systems.&#x20;
* Run collectors on AV excluded or non-domain-joined machines using the `runas` command with `/netonly` to authenticate without joining domain
  * `runas /netonly /user:DOMAIN\username cmd.exe` launches a process pretending to be that user only for network authentication, not locally. The launched process (e.g., cmd.exe) still runs under your local machine's context, but when it makes network requests, it uses the provided domain credentials.

### Sharphound

Used on Windows systems.

`.\SharpHound.exe --CollectionMethods All --Domain tryhackme.loc --ExcludeDCs`

### Bloodhound-python

Used on Linux. Helpful when you don't have a domain-joined Windows machine.

`bloodhound-python -u asrepuser1 -p qwerty123! -d tryhackme.loc -ns 10.211.12.10 -c All --zip`



