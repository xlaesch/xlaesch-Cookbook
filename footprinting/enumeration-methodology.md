A penetration test must need a standardized methodology. The following is a good place to start
![[footprinting-methodology.png]]

| **Layer**                | **Description**                                                                                        | **Information Categories**                                                                         |
| ------------------------ | ------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| `1. Internet Presence`   | Identification of internet presence and externally accessible infrastructure.                          | Domains, Subdomains, vHosts, ASN, Netblocks, IP Addresses, Cloud Instances, Security Measures      |
| `2. Gateway`             | Identify the possible security measures to protect the company's external and internal infrastructure. | Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Network Segmentation, VPN, Cloudflare                  |
| `3. Accessible Services` | Identify accessible interfaces and services that are hosted externally or internally.                  | Service Type, Functionality, Configuration, Port, Version, Interface                               |
| `4. Processes`           | Identify the internal processes, sources, and destinations associated with the services.               | PID, Processed Data, Tasks, Source, Destination                                                    |
| `5. Privileges`          | Identification of the internal permissions and privileges to the accessible services.                  | Groups, Users, Permissions, Restrictions, Environment                                              |
| `6. OS Setup`            | Identification of the internal components and systems setup.                                           | OS Type, Patch Level, Network config, OS Environment, Configuration files, sensitive private files |

