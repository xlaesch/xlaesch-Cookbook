# SSRF

⇒ Server-Side Request Forgery, vuln that allows webserver to make additional or edited HTTP request to resource of attacker’s choosing

* regular SSRF: data returned to attacker’s screen
* Blind SSRF: no information returned to attacker’s screen
* Manipulating the api request parameters with an empty parameter (e.g. `?x=` ⇒ x param does not exist in API and is therefore ignored) can give information that wasn’t usually available.
* changing the api url can reveal what API keys were being sent

SSRF can be found when:

* when a full URL is used in a param in the address bar
* hidden field in a form
* partial URL such as just the hostname or only the path to the URL

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Defeating common SSRF defenses:

* **Deny list:** requests are all accepted except resources/IPs that are part of a list or matching a pattern (e.g. restrict endpoint access to the [localhost](http://localhost) because it contains administrative/performance data ⇒ usually `127.0.0.1` )
  * use alternative localhost references such as `0` , `0.0.0.0` , or `127.*.*.*`
  * subdomains that have a DNS records which resolves to the [localhost](http://localhost) such `127.0.0.1.nip.io`
* **Allow List:** all requests denied unless they appear on a list or match pattern (e.g. URL must begin with [http://website.thm](http://website.thm)
  * can be bypassed with a subdomain
* **Open Redirect:** endpoint on the server where the website visitor gets redirected to another website
  * can redirect HTTP requests to an attacker’s domain
