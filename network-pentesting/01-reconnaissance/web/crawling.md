---
tags: [recon, web, osint]
aliases: [Crawling]
---
Crawling is the automated process of systematically browsing the World Wide Web. Crawlers start with a seed URL, fetches this page, parses through the content, and extracts all its links. Then adds the links to the queue and crawls them, repeating the process iteratively.

Crawlers extracts anything from links, comments, metadata, or sensitive files.

# Crawl Strategy

Some common crawling algorithms:
- Breadth-first crawling prioritizes exploring a website's width before going deep.
- Depth-first crawling prioritizes depth over breadth.

# robots.txt

`robots.txt` is a simple text file in the root directory of a website that gives instructions to the bots or which parts of website they can and cannot crawl. It targets specific user-agents. It also haws directives which are the specific instructions:

| Directive     | Description                                                                                                        | Example                                                         |
| ------------- | ------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------- |
| `Disallow`    | Specifies paths or patterns that the bot should not crawl.                                                         | `Disallow: /admin/` (disallow access to the admin directory)    |
| `Allow`       | Explicitly permits the bot to crawl specific paths or patterns, even if they fall under a broader `Disallow` rule. | `Allow: /public/` (allow access to the public directory)        |
| `Crawl-delay` | Sets a delay (in seconds) between successive requests from the bot to avoid overloading the server.                | `Crawl-delay: 10` (10-second delay between successive requests) |
| `Sitemap`     | Provides the URL to an XML sitemap for more efficient crawling.                                                    | `Sitemap: https://www.example.com/sitemap.xml`                  |

Web reconnaissance of this file can uncover hidden directories, mapping website structure , and detect crawler traps.

# .well-known

Another interesting file is the .well-known directory that servers use as a standardized directory within a website's root domain. It centralizes critical metadata, including configuration files and information related to its services. Some notable examples are:

| URI Suffix                     | Description                                                                                           | Status      | Reference                                                                                                                                                                          |
| ------------------------------ | ----------------------------------------------------------------------------------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `security.txt`                 | Contains contact information for security researchers to report vulnerabilities.                      | Permanent   | RFC 9116                                                                                                                                                                           |
| `/.well-known/change-password` | Provides a standard URL for directing users to a password change page.                                | Provisional | [https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri](https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri) |
| `openid-configuration`         | Defines configuration details for OpenID Connect, an identity layer on top of the OAuth 2.0 protocol. | Permanent   | [http://openid.net/specs/openid-connect-discovery-1_0.html](http://openid.net/specs/openid-connect-discovery-1_0.html)                                                             |
| `assetlinks.json`              | Used for verifying ownership of digital assets (e.g., apps) associated with a domain.                 | Permanent   | [https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md](https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md)         |
| `mta-sts.txt`                  | Specifies the policy for SMTP MTA Strict Transport Security (MTA-STS) to enhance email security.      | Permanent   | RFC 8461                                                                                                                                                                           |

.well-known can be used for discovering endpoints and configuration details that can be further tested during a pentest.

## Related

- [[content-discovery]]
- [[REORG_SOURCE_MAP]]
- [[local|Local File Inclusion]]
- [[linux-old]]

