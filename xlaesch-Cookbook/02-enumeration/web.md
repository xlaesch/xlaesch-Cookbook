---
tags: [enum]
aliases: [Web]
---

# Nmap Web Discovery

```shell
# common web ports
nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list
```
# EyeWitness

> EyeWitness is superseded by the actively maintained `gowitness` — prefer it for new work.

Can take an XML output from Nmap or Nessus and create a report with screenshots of each web application present on the various ports using Selenium. Will also try categorizing the applications, fingerprint them and suggest default credentials. 

```shell
# take screenshots using nmap xml output
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness
```

# gowitness

Maintained successor to EyeWitness/Nessus-style screenshotting. Uses headless Chrome, so it respects `/etc/hosts` entries — useful for screenshotting vhosts that all resolve to one IP. Input files expect one URL per line; include the scheme to be explicit.

```shell
# save discovered vhosts to a file, one URL per line
cat > vhosts.txt << 'EOF'
http://inlanefreight.local
http://blog.inlanefreight.local
http://careers.inlanefreight.local
http://dev.inlanefreight.local
http://gitlab.inlanefreight.local
http://support.inlanefreight.local
http://tracking.inlanefreight.local
EOF

# screenshot each vhost, full page (-F), and write results to the local db for the report server
gowitness scan file -f vhosts.txt --screenshot-fullpage --write-db

# browse results in the gallery at http://localhost:7171
gowitness report server
```

> On gowitness v2 the syntax is `gowitness file -f vhosts.txt`. Duplicate lines with `https://` for hosts serving TLS.

# Aquatone

Similar to EyeWitness.

```shell
cat web_discovery.xml | ./aquatone -nmap
```

## Related

- [[port-scanning]]
- [[content-discovery]]
- [[notetaking]]
- [[fingerprinting|Web Fingerprinting]]

