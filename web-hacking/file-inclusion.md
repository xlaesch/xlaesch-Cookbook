---
description: >-
  A vulnerability mainly in the input validation that allows leaked data to be
  accessed.
---

# File Inclusion

## Local File Inclusion (LFI)

The ability to access files from the local filesystem. Typically through the use of dangerous PHP functions `include`,`require`,`include_once`,`require_once`&#x20;

#### Input Validation Bypasses

* In PHP <5.34, we could include the null byte (`%00`)character to tell the PHP code to ignore the characters that followed.
* We can also bypass PHP filters by craftring a blacklisted string that when sanitized reveals a second, identical blacklisted string. This is because some PHP sanitization functions aren't recursive, so they just scan left to right.
* PHP filters might only act on specific request types if `$_REQUEST` is being used. So changing the request type to `POST`,`GET`,`COOKIE` might bypass filtering.

#### Directory Traversal

When user input is passed to a function that reads the directory.  We can use `..` to move up the directory. Check [#useful-file-locations](../privilege-escalation/linux.md#useful-file-locations "mention")  to view relevant file locations.

## Remote File Inclusion (RFI)

The ability to include remote files into a vulnerable application.

#### Techniques

* Injecting an external URL into the `include` function with `allow_url_fopen` `on` can allow an attack to set up an HTTP server and serve a PHP payload that will be run by the PHP server.



