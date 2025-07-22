---
icon: shield-quartered
---

# Principles of Security

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

### **Key Acronyms**

### Security Frameworks

**CIA Triad** for data:

* Confidentiality: protection of data from unauthorized access and  misuse.
* Integrity: Information is kept accurate and consistent unless authorized changes are made.
* Availability: data is available and accessible by the user (e.g. 99.9% uptime)

**AAA Triad** for access:

* Authentication: Users providing information about who they are.
* Authorization: Users can be granted privileges to access certain area of a network or system.
* Accounting: Keeping track of user activity while users are logged in to a network.

**Bell-LaPadula Model:** "No write down, No read up".  Subjects can only read data at or below their clearance level and write data to objects at or above their clearance level. Used in military organizations to prevent unauthorized disclosure of information.

**Biba Model:** "No write up, No read down". Subjects can create or write content to objects at or below their level but can only read contents of objects above the subject's level. Prevents subjects from being"contaminated" by information that is considered less trustworthy, also prevents subject from corrupting data that has a higher integrity standing. Used in businesses with the CIA triad in mind.&#x20;

### Vulnerabilities

A weakness of flaw in the design that can be exploited to gain access to unauthorised information or actions. Classified under Common Vulnerability and Exposure (CVE) with format `CVE-YEAR-IDNUMBER`

**Common Vulnerability Scoring System (CVSS)**: addresses how easy it is to exploit the vulnerability, if exploits exist and how it interferes with the CIA triad.

**Vulnerability Priority Rating (VPR):** risk driven, scored based on risk to the organisation itself. More dynamic than CVSS but closed source and less widely adopted.

**Vulnerability and Exploit Databases:**

* National Vulnerability Database (NVD). Not ideal for finding vulnerabilities for specific applications or scenarios.
* Exploit-DB. Includes Proof of Concepts (PoC)
* Rapid7. Much like Exploit-DB but also has an exploit database.
* Github. Research share PoC's on Github.
* `searchsploit` is a CLI tool that is an offline copy of exploit-db
