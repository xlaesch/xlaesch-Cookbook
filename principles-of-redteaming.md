---
icon: skull
---

# Red Teaming

## Pen-Testing Guidelines and Methodologies

**Rules of Engagement (ROE)** document. Document created in the initial stages of a pen-test. There are 3 main sections:

* Permission: stating for permissions for engagement, so the team responsible is not subject to legal repercussions.
* Test Scope: which targets the engagement should apply to. There are 3 primary scopes:
  * Black-Box Testing: No information about inner working of the target application or service. You pretend you are the regular user.
  * Grey-Box Testing: Limited information about internal components of the target application or service. You still pretend as if you are a regular user.
  * White-Box Testing: Knows application or service logic and you test the internal components.
* Rules: techniques permitted during the engagement.

**General Methodology Framework:**

1. Information Gathering: Collect as much publicly available information on the target systems.
2. Enumerating/Scanning: Discover applications and services on the target systems.
3. Exploitation: Leverage vulnerabilities discovered on a system or application, typically done with public exploits or poor application logic.
4. Privilege Escalation: Expand access to a system.
5. Post-Exploitation: Determine other hosts to target (pivoting), gather additional information, cover tracks, and report findings.

{% hint style="info" %}
Some frameworks that can be followed include:

* Open Source Security Testing Methodology Manual (OSSTMM)
* Open Web Application Security Project (OWASP)
* National Institute of Standard and Technology (CIST) Cybersecurity Framework
{% endhint %}

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

***

## Writing Pen-Test Reports

Reports are the only lasting output of an engagement. We must understand our audiences and how to communicate with each.

### Styling

* No ambiguity, simple and direct language.
  * Use same terminology, formatting and structure.
  * Write in past tense.
  * No first person language, neutral observer.
  * Mask sensitive info (no passwords or private data).

### Composition

**Audiences:**

* Technical Stakeholders: help the technical team understand the root cause of discovered vulnerabilities and how to remediate them.
* Security Stakeholders: provide guidance to the security personnel to prioritise remediation efforts and decide which risks have to be addressed before a system goes, and which risks can be accepted.
* Business Stakeholders: Not technical rather business-driven. What business impact does our findings have.

**Sections (Ordered):**

* Summary: high-level view of the assessment for the Business and Security Stakeholders. Often written last. Should try and draw link between issues found and limit technical jargon.
  * Overview: What was tested? What type of system or application? What were the goals? Detail the scope and how much coverage was achieved.
  * Results: What did the assessment uncover? Is the target secure or not? What if issues were found if not?
  * Impact: What is the real-world impact if the issues remain unaddressed? How can it be exploited by real-life threat actors? For&#x20;
  * Remediation Direction: At a high level, what actions should the organization take? Is it a major investment or a quick fix?
* Vulnerability Write-Ups: intended for the Technical Stakeholders, each issue found getting itws own write-up.
  * Title: Short descriptive heading.
  * Risk Rating: Always rated in isolation (not accounting for other vulnerabilities). Should follow the organization's rating scale.
  * Summary: Brief explanation and its impact in plain language.
  * Background: More context to the vulnerability and why it matters, most developers are not security experts, so include some guidance to help understand.
  * Technical Details & Evidence: Where and how the issues was found.
  * Impact: What an attacker could realistically do with this.
  * Remediation Advice: Clear actionable steps to resolve the issues. Try and address the vulnerabilities isssue at its core.
  * References: optional links to vendor documentation
* Appendices: intended for the Security Stakeholders, with the supporting details that don't fit inside the main reports:
  * Assessment Scope: Establish how close the assessment was to what was originally scoped in the ROE document
  * Assessment Artifacts: List out any changes that were made during testing. Cleaning up after an assessment is ideal it might not always be possible to remove all artifacts.

