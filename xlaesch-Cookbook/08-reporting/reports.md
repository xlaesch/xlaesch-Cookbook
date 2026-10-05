---
tags: [reporting, reports]
aliases: [Reports]
---
The main deliverable that a client is paying for when they contract a firm to perform a penetration test.
# Components of a Report

Everything in a report should have a reason for being there.
## Attack Chain

Details the way in we gained a foothold, moved laterally, and compromised the domain. Start with a summary of the attack chain and then walk through each step along with supporting command output and screenshots to show the attack chain as clearly as possible.

## Executive Summary

Intended audience  typically the person responsible for allocating the budget for fixing the issues we discovered. 
- It should be obvious, but this should be written for someone who isn't technical at all. The typical barometer for this is "if your parents can't understand what the point is, then you need to try again" (assuming your parents aren't CISOs or sysadmins or something of the sort).
- The reader doesn't do this every day. They don't know what Rubeus does, what [[password-spraying|password spraying]] means, or how it's possible that tickets can grant different tickets (or likely even what a ticket is, aside from a piece of paper to enter a concert or a ballgame).
- This may be the first time they've ever been through a penetration test.
- Much like the rest of the world in the instant gratification age, their attention span is small. When we lose it, we are extraordinarily unlikely to get it back.
- Along the same lines, no one likes to read something where they have to Google what things mean. Those are called distractions.
- `When talking about metrics, be as specific as possible.`
- `It's a summary. Keep it that way.`
- `Describe the types of things you managed to access`
- `Describe the general things that need to improve to mitigate the risks you discovered`
- `If you're feeling brave and have a decent amount of experience on both sides, provide a general expectation for how much effort will be necessary to fix some of this`

Do not:
- `Name or recommend specific vendors`
- `Use Acronyms.`
- `Spend more time talking about stuff that doesn't matter than you do about the significant findings in the report`
- `Reference a more technical section of the report.`

### Vocabulary Changes

- `VPN, SSH` - a protocol used for secure remote administration
- `SSL/TLS` - technology used to facilitate secure web browsing
- `Hash` - the output from an algorithm commonly used to validate file integrity
- `Password Spraying` - an attack in which a single, easily-guessable password is attempted for a large list of harvested user accounts
- `Password Cracking` - an offline password attack in which the cryptographic form of a user’s password is converted back to its human-readable form
- `Buffer overflow/deserialization/etc.` - an attack that resulted in remote command execution on the target host
- `OSINT` - Open Source Intelligence Gathering, or hunting/using data about a company and its employees that can be found using search engines and other public sources without interacting with a company's external network
- `SQL injection/XSS` - a vulnerability in which input is accepted from the user without sanitizing characters meant to manipulate the application's logic in an unintended manner

## Summary of Recommendations

List our short, medium and long-term recommendations based on our findings and the current state of the client's environment. Must use the context of the business's client's business, security budget, etc. to make accurate recommendations. Should tie each recommendation back to a specific finding. 

## Findings

Explain what we found how we exploited them, and give the client guidance on how to remediate the issues. Each finding should have the same general type of information that should be customized to your client's specific circumstances. Should include
- Description of the finding and what platform(s) the vulnerability affects
- Impact if the finding is left unresolved
- Affected systems, networks, environments, or applications
- Recommendation for how to address the problem
- Reference links with additional information about the finding and resolving it
- Steps to reproduce the issue and the evidence that you collected

We want to present evidence in a way that is understandable and actionable to the client. Our audience may not be as technical as we are so:
- Break each step into its own figure. If you perform multiple steps in the same figure, a reader unfamiliar with the tools being used may not understand what is taking place, much less have an idea of how to reproduce it themselves.
- If setup is required (e.g., Metasploit modules), capture the full configuration so the reader can see what the exploit config should look like before running the exploit. Create a second figure that shows what happens when you run the exploit.
- Write a narrative between figures describing what is happening and what is going through your head at this point in the assessment. Do not try to explain what is happening in the figure with the caption and have a bunch of consecutive figures.
- After walking through your demonstration using your preferred toolkit, offer alternative tools that can be used to validate the finding if they exist (just mention the tool and provide a reference link, don't do the exploit twice with more than one tool).

### Remediation

Should be very precise and with the goal of allowing the client to do as little research as possible.

### References

Each finding should include an external reference for further reading.

A vendor-agnostic source is helpful. Obviously, if you find an ASA vulnerability, a Cisco reference link makes sense, but I wouldn't lean on them for a writeup on anything outside of networking. If you reference an article written by a product vendor, chances are the article's focus will be telling the reader how their product can help when all the reader wants is to know how to fix it themselves.

## Appendices

### Static

- Scope of the assessment
- Methodology - repeatable process you follow to ensure that your assessment are thorough and consistent.
- Severity Ratings - some sort of criteria to meet your severity definitions.
- Biographies - bio about the personnel performing the assessment 
### Dynamic

- Exploitation Attempts and Payloads: artifacts left behind, details about custom payloads, etc.
- Compromised Credentials: List the compromised accounts.
- Configuration Changes: any changes made in the client environment.
- Additional Affected Scope
- Information Gathering: additional data to help the client understand their external footprint
- Domain Password Analysis: Key stats from passwords (hashes found, percentage cracked, etc.)

# Extra

- Aim to tell a story with your report. Why does it matter that you could perform Kerberoasting and crack a hash? What was the impact of default creds on X application?
- Write as you go. Don't leave reporting until the end. Your report does not need to be perfect as you test but documenting as much as you can as clearly as you can during testing will help you be as comprehensive as possible and not miss things or cut corners while rushing on the last day of the testing window.
- Stay organized. Keep things in chronological order, so working with your notes is easier. Make your notes clear and easy to navigate, so they provide value and don't cause you extra work.
- Show as much evidence as possible while not being overly verbose. Show enough screenshots/command output to clearly demonstrate and reproduce issues but do not add loads of extra screenshots or unnecessary command output that will clutter up the report.
- Clearly show what is being presented in screenshots. Use a tool such as [Greenshot](https://getgreenshot.org/) to add arrows/colored boxes to screenshots and add explanations under the screenshot if needed. A screenshot is useless if your audience has to guess what you're trying to show with it.
- Redact sensitive data wherever possible. This includes cleartext passwords, password hashes, other secrets, and any data that could be deemed sensitive to our clients. Reports may be sent around a company and even to third parties, so we want to ensure we've done our due diligence not to include any data in the report that could be misused. A tool such as `Greenshot` can be used to obfuscate parts of a screenshot (using solid shapes and not blurring!).
- Redact tool output wherever possible to remove elements that non-hackers may construe as unprofessional (i.e., `(Pwn3d!)` from CrackMapExec output). In CME's case, you can change that value in your config file to print something else to the screen, so you don't have to change it in your report every time. Other tools may have similar customization.
- Check your Hashcat output to ensure that none of the candidate passwords is anything crude. Many wordlists will have words that can be considered crude/offensive, and if any of these are present in the Hashcat output, change them to something innocuous. You may be thinking, "they said never to alter command output." The two examples above are some of the few times it is OK. Generally, if we are modifying something that can be construed as offensive or unprofessional but not changing the overall representation of the finding evidence, then we are OK, but take this on a case-by-case basis and raise issues like this to a manager or team lead if in doubt.
- Check grammar, spelling, and formatting, ensure font and font sizes are consistent and spell out acronyms the first time you use them in a report.
- Make sure screenshots are clear and do not capture extra parts of the screen that bloat their size. If your report is difficult to interpret due to poor formatting or the grammar and spelling are a mess, it will detract from the technical results of the assessment. Consider a tool such as Grammarly or LanguageTool (but be aware these tools may ship some of your data to the cloud to "learn"), which is much more powerful than Microsoft Word's built-in spelling and grammar check.
- Use raw command output where possible, but when you need to screenshot a console, make sure it's not transparent and showing your background/other tools (this looks terrible). The console should be solid black with a reasonable theme (black background, white or green text, not some crazy multi-colored theme that will give the reader a headache). Your client may print the report, so you may want to consider a light background with dark text, so you don't demolish their printer cartridge.
- Keep your hostname and username professional. Don't show screenshots with a prompt like `azzkicker@clientsmasher`.
- Establish a QA process. Your report should go through at least one, but preferably two rounds of QA (two reviewers besides yourself). We should never review our own work (wherever possible) and want to put together the best possible deliverable, so pay attention to the QA process. At a minimum, if you're independent, you should sleep on it for a night and review it again. Stepping away from the report for a while can sometimes help you see things you overlook after staring at it for a long time.
- Establish a style guide and stick to it, so everyone on your team follows a similar format and reports look consistent across all assessments.
- Use autosave with your notetaking tool and MS Word. You don't want to lose hours of work because a program crashes. Also, backup your notes and other data as you go, and don't store everything on a single VM. VMs can fail, so you should move evidence to a secondary location as you go. This is a task that can and should be automated.
- Script and automate wherever possible. This will ensure your work is consistent across all assessments you perform, and you don't waste time on tasks repeated on every assessment.

# Report Types

### Vulnerability Assessment

Involves running an automated scan of an environment to enumerate vulnerabilities. Authenticated or unauthenticated. No exploitation attempted. 

### Internal vs External

External scan is performed from the perspective of an 
anonymous user on the internet. 

Internal scan is conducted from the perspective of a scanner on the internal network and investigates hosts from behind the firewall.

## Penetration Testing

A penetration test goes beyond automated scans and leverages scan data to help guide exploitation. 

A penetration test may be performed from various perspectives.
- "black box" where we have no more information than the name of the company during an external pentest
- "grey box" given in-scope IP addresses
- "white box" given credentials, source code, etc.

We may perform "evasive testing" throughout the assessment. We try to remain undetected for as long as possible.
## Intern-Disciplinary Assessments

Some assessments may require other people or other environments.

- Purple team: combined effort between the blue and red teams, most commonly a penetration tester and an incident responder.
- Cloud focused: Very similar to convetional penetration test but we may have someone providing information of the cloud architecture.
- IoT Testing: network, cloud, and application and requires far more specialization rather than relying on one person with only basic knowledge.
- Web Application Penetration Testing
- Hardware Penetration Testing

## Final Report

### Post-Remeditation

Clients will often request that the findings found should be tested again after the company has had the opportunity to correct them. This does not involve redoing the entire assessment but retesting only the findings found in the original assessment.

### Attestation Report

A document suitable for their vendors or customers who require evidence that they've had a penetration test done. Client will not want to hand over the specific technical details of all the findings.

## Vulnerability Notifications

We might uncover a critical flaw that requires us to stop work and inform our clients of an issue so they can decide if they would like to issue an emergency. This should be done for any finding that is directly exploitable and exposed to the internet. Limited amounts of fluff in these documents.

## Other Deliverables

Some other relevant deliverables might be
- Slide deck to be given at several levels of the org.
- Spreadsheet of Findings 

## Related

- [[notetaking]]
- [[sqlmap]]
- [[osint]]
- [[pivoting]]

