---
tags: [reporting, notetaking]
aliases: [Notetaking]
---
Practically essential in an engagement. A general recommended structure is as follows
- Attack path - outline of the entire path, shoutlined as detailed as possible using screenshot and command output.
- Credentials
- Findings - create a subfolder for each finding and then writing a narrative and saving it in the folder along with any evidence.
- Vulnerability Scan Research
- Service Enumeration Research - what services have been investigated, etc.
- Web Application Research
- AD Enumeration Research
- OSINT
- Administrative Information - contact info of the Project Managers (PM) or client Points of Contact (POCs), objectives, flags defined in the RoE, or simply a to-do list.
- Scoping information - in-scope IP addresses, CIDR range, etc.
- Activity Log - high-level log of everything you did
- Payload log - track the payloads (with a file hash)

We must log all scanning and attack attempts and keep raw tool output wherever possible. For terminal logging we can use tmux logging that will save everything single that we type into a Tmux pane. 

# Artifacts

We should be tracking at a minimum which payload was used, which host it was used on, file path, and whether it needs to be cleaned up  by the client. A file hash is recommended as well.

If we create accounts or modify system settings we should keep track of those as well.
- IP address of the host(s)/hostname(s) where the change was made
- Timestamp of the change
- Description of the change
- Location on the host(s) where the change was made
- Name of the application or service that was tampered with
- Name of the account (if you created one) and perhaps the password in case you are required to surrender it

# Evidence

Clients pay for report deliverables. We should keep a general structure for storage of evidence on a machine. It can such framework:
- Admin - Scope of Work, meeting notes, etc.
- Deliverables - spreadsheets, slide decks, etc.
- Evidence 
	- Findings - a folder for each finding you plan to include in the report to keep your evidence for each finding in a container
	- Scans
		- Vulnerability scans - files from the scanner
		- Service enumeration - nmap, rumble, etc.
		- Web - Burp state files, EyeWitness
		- Ad Enumeration, JSON files from Bloodhound
	- Notes
	- OSINT
	  Wireless
	- Logging Output - output from tmux, etc.
	- misc files - web shells payloads, etc.
- Retest - retest the previously discovered findings.

```shell
xlaesch@htb[/htb]$ mkdir -p ACME-IPT/{Admin,Deliverables,Evidence/{Findings,Scans/{Vuln,Service,Web,'AD Enumeration'},Notes,OSINT,Wireless,'Logging output','Misc Files'},Retest}

# output
xlaesch@htb[/htb]$ tree ACME-IPT/

ACME-IPT/
├── Admin
├── Deliverables
├── Evidence
│   ├── Findings
│   ├── Logging output
│   ├── Misc Files
│   ├── Notes
│   ├── OSINT
│   ├── Scans
│   │   ├── AD Enumeration
│   │   ├── Service
│   │   ├── Vuln
│   │   └── Web
│   └── Wireless
└── Retest

```

# Formatting

Credentials and Personal Identifiable Information should be redacted in screenshots. 

## Screenshots

We should try to use terminal output over screenshots of the terminal. You can cut out unnecessary output and mark the removed portion with `<SNIP>`.  Never using blurring or pixelation instead use black bars over the text to redact.

## Terminal

Terminal outputted credentials should be redacted including password hashes. Replace with a placeholder.

## Related

- [[reports]]
- [[osint]]
- [[xlaesch-Cookbook/07-active-directory/old/bloodhound]]
- [[06-post-exploitation/credential-access/credential-hunting|Credential Hunting]]

