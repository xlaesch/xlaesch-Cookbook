---
description: Insecure Direct Object Reference (IDOR)
---

# IDOR

An Access Control vulnerability where a web server uses user-supplied input to retrieve objects and trust this input data too much with a lack of server-side validation.

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

IDs can be encoded or hashed, but that shouldn't stop an attacker. A common way to check if IDOR exists by creating two accounts and swapping the IDs between the two and seeing if access is still allowed.
