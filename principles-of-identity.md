---
icon: fingerprint
---
- **Provisioning / SCIM client** (inside Entra) – pulls HR changes, remaps attributes, then drives CRUD calls over the SCIM 2.0 REST API.
- **Identity & Access Management layer (Entra ID)** – the cloud directory that issues tokens for apps and acts as policy brain.
- **Active Directory on-prem** – still authoritative for many orgs; a lightweight agent syncs it into Entra so on-prem changes don’t get lost.
- **Target SaaS / line-of-business apps** – each exposes a SCIM server endpoint and receives the “create / update / delete” payloads.

**assertion:** digitally signed statement issues by an IdP saying: I confirm this user is who they say they are, here’s their usersnames

Examples: SAML, OIDC, OAuth

**Service Provider:** app users wants to access (Google Drive, Salesforce, etc)

**Tenant:** dedicated, isolated instance of a cloud service (like Okta, Entra ID, or Google Workspace) that contains all the users, configurations, and data for a specific organization.

**Federation:** one system (the service provider) trust another system (Idp) to authenticate users

### SAML (Security Assertion Markup Language)

XML based protocol over HTTP used for SSO between an IdP and Service Provider, digitally signed

Contains:

- **Subject**: Who the user is (e.g., `user@company.com`)
- **Attributes**: Metadata like name, roles, department
- **Conditions**: Time limits, recipient domain
- **Signature**: Digital signature from IdP to ensure authenticity

**Signature Process**

IDP Side

1. **Create the `<SignedInfo>` block**
    
    → This XML block lists what part of the SAML message is being signed and what algorithms are used.
    
2. **Hash the referenced part** (e.g., the SAML Assertion)
    
    → Using something like SHA-256 → result is a fixed-size digest.
    
3. **Insert that digest into `<SignedInfo>`**
    
    → This lets the SP later know what hash value to expect.
    
4. **Hash the `<SignedInfo>` block itself**
    
    → You’re preparing to sign this block.
    
5. **Sign the hash using the private key**
    
    → This means:
    
    `SignatureValue = Encrypt_with_PrivateKey(SHA-256(<SignedInfo>))`
    
    → This is the actual "digital signature" — a scrambled version of the hash that only the private key can produce.
    
6. **Insert the `SignatureValue` into the SAML response**
    
    → Along with the rest of the SAML message, this gets sent to the Service Provides
    

SP Side

1. **Receive the SAML response, including `<SignedInfo>` and `SignatureValue>`**
    
    → The whole message comes through the browser.
    
2. **Hash the received `<SignedInfo>` block**
    
    → Using the same algorithm (e.g., SHA-256)
    
3. **Decrypt the `SignatureValue` using the IdP’s public key**
    
    → This reveals the original hash the IdP signed.
    
4. **Compare the two hashes**
    
    → If the SP's calculated hash matches the decrypted one from the IdP:
    
    ✅ Signature is valid
    
    ❌ Otherwise, reject the message
    

When first creating the relationship between an SP and Idp, there is a metadata exchange where the SP imports the IdP’s public certificate (public key)

![image.png](attachment:848bf7f2-e7e4-4d0e-abd0-da8bb5af8966:image.png)

### SCIM

basically an API, protocol that standardized how identity information is between one entity and another with the goal of making it easier for for IT admins to provision users (create, maintain or update people’s accounts and give them permission to access cloud-based (SaaS) apps they need).

- **System:** creates a common format for how identity is exchanged
- **Cross-Domain:** securely communicates identity across platforms
- **Identity-Management**: automates flow of info between an identity provider (Okta, Entra …) or Identity and Access Management (IAM) system and cloud-based apps

It uses JSON, to communicate accross domains and a REST API to perform the actions needed to manage identity lifecycles. These are it’s operations:

- create ⇒ add new users
- read ⇒ retrieve or search for info
- update ⇒ sync updated identity info between client and apps
- delete ⇒ de-provision identities

### SSO (Single Sign-On)

SSO service is built into most IdPs

user has to enter login credentials one time on a single pag eto access all of their SaaS apps.

How it works:

- users signs in to an SSO serivce, service issues an authentication toke that remembers that the user is verified
    - something stored in browser (cookie) or in SSO service’s servers
- token is passed to every app the user is allowed into

**IdP** = The person at the door checking your ID.

**SSO** = The VIP wristband that gets you into every room after that check.

### On-Prem vs Cloud Active Directory

**On-Prem**

physical/virtual Windows Servers that uses

- Domain Controllers (DCs) server that auths users, group policy managers
- LDAP (structure of directory), Kerberos (authentication tickets), GP, NTLM protocols and mechanisms
- Runs entirely within network (LAN or VPN)

How it Works

- Devices and users **join the domain** (e.g., CORP.local).
- Login is authenticated **against local Domain Controllers**.
- Policies are **pushed using Group Policy Objects (GPOs)**.
- Everything is tightly integrated with **Windows environments**.

**Cloud Active Directory**

identity managed in the cloud, no on-prem infra

No Group Policy ⇒ Endpoint Manager required

When a user logins to their Windows device:

- Windows either caches Azure AD credentials to work offline or verifies via Azure AD over the internet

**How they work together**

Azure AD Connect Agent ⇒ tool that syncs user account and attributes from on-prem AD → Azure AD