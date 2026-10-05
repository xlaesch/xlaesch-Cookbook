
### NTLM

- LM and NT are hash algorithms used to store password credentials. Note: they don't uses salting.
- NTLMv1 and NTLMv2 are authentication protocols built on top of these hashes.
- Kerberos is however the preferred authentication method.

- Lan Manager (LM, old) hashes are stored in the SAM database. It is turned off since 2008. 
	- Maximum of 14 characters
	- The hashing algorithm splits the password into two seven-character chunks so an attacker only has to brute force seven characters twice instead of 14 characters.
	- Can be disallowed using Group Policy

- NTHash (NTLM) hashes
	- Challenge response authentication protocol wiht three messages to authenticate,
	1. Client sends a `NEGOTIATE_MESSAGE` to the server.
	2. Server responds with `CHALLENGE_MESSAGE` that is 8-byte random number
	3. Client responds with an `AUTHENTICATE_MESSAGE` that is 24-byte response.
	- Can still be brute forced quite easily still and vulnerable to pass-the-hash style attacks

NTLM hash looks like this
```shell-session
Rachel:500:aad3c435b514a4eeaad3b935b51304fe:e46b9e548fa0d122de7f59fb6d48eaa2:::
```
- Rachel is username
- 500 is RID (admin)
- `aad3c435b514a4eeaad3b935b51304fe` is LM hash, if they are disabled on system then it is useless
- `e46b9e548fa0d122de7f59fb6d48eaa2` is the NT hash

- NTLMv2 unlike NTLMv1 sends two responses to the 8-byte challenge received by the server to prevent spoofing attacks
	- a 16-byte HMAC-MD5 of the challenge
	- Variable-length client challenge including current time, 8-byte random value and domain name

- Domain Cached Credentials (MSCache2) solve the potential issue if a domain joined host cannot communicate with DC. The host saves the last ten hashes for any domain joined users in `HKEY_LOCAL_MACHINE\SECURITY\Cache`. Cannot be used in pass-the-hash attack. And are very hard to crack.