LM and NT are unsalted hash algorithms used to store password credentials. NTLMv1 and NTLMv2 are authentication protocols built on these hashes; Kerberos is the preferred authentication method.

# LM Hashes

LAN Manager (LM) hashes are stored in the SAM database and have been disabled since 2008. They can be disallowed through Group Policy.

- Maximum password length: 14 characters.
- The algorithm splits the password into two seven-character chunks, allowing an attacker to brute-force each chunk separately rather than all 14 characters.

# NT Hashes and NTLM Authentication

NTLM uses a challenge-response exchange with three messages:

| **Step** | **Message** | **Description** |
| --- | --- | --- |
| 1 | `NEGOTIATE_MESSAGE` | Sent by the client to the server. |
| 2 | `CHALLENGE_MESSAGE` | Server response containing an eight-byte random number. |
| 3 | `AUTHENTICATE_MESSAGE` | Client response containing a 24-byte response. |

> NT hashes can still be brute-forced and are vulnerable to pass-the-hash attacks.

## Hash Format

```text
Rachel:500:aad3c435b514a4eeaad3b935b51304fe:e46b9e548fa0d122de7f59fb6d48eaa2:::
```

| **Field** | **Description** |
| --- | --- |
| `Rachel` | Username. |
| `500` | Administrator RID. |
| `aad3c435b514a4eeaad3b935b51304fe` | LM hash; not useful when LM hashes are disabled. |
| `e46b9e548fa0d122de7f59fb6d48eaa2` | NT hash. |

# NTLMv2

Unlike NTLMv1, NTLMv2 sends two responses to the server's eight-byte challenge to prevent spoofing attacks:

- A 16-byte HMAC-MD5 of the challenge.
- A variable-length client challenge containing the current time, an eight-byte random value, and the domain name.

# Domain Cached Credentials

Domain Cached Credentials (MSCache2) allow logon when a domain-joined host cannot communicate with a domain controller. The host saves the last ten hashes for domain users in `HKEY_LOCAL_MACHINE\SECURITY\Cache`.

> MSCache2 hashes cannot be used for pass-the-hash attacks and are difficult to crack.
