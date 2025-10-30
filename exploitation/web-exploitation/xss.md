# XSS

Cross-site scripting, injection attacker where malicious Javascript gets injected into a web app and gets returned to users.

Types of payloads:

* Proof of concept: show that you achieved CSS on a website, usually with `alert`
* Session Stealing: details of user session ⇒ login tokens, kept in cookies, take over the session and use to login
* Key Logger: anything typed gets sent to a website used by hacker
* Business Logic: calling a particular network or JS function

### Reflected XSS

* user-supplied data in an HTTP request is included in webpage source without validation. Not persistent.
  * attacker could send links or embed them into iframe containing JS payload

{% hint style="info" %}
Suppose a website has a search function which receives the user-supplied search term in a URL parameter:

`https://insecure-website.com/search?term=gift`\
The application echoes the supplied search term in the response to this URL:

You searched for: gift

Assuming the application doesn't perform any other processing of the data, an attacker can construct an attack like this:

`https://insecure-website.com/search?term=/+Bad+stuff+here...+/` \
This URL results in the following response:

You searched for: `/* Bad stuff here... */`

If another user of the application requests the attacker's URL, then the script supplied by the attacker will execute in the victim user's browser, in the context of their session with the application.
{% endhint %}

#### Methodology

* **Test every endpoint.** Every parameter within the URL query string / file path. Even HTTP Headers.
* For each entry point use random values (that won't trigger any form of input filtering)
* **Determine the reflection context.** Could be between HTML tags, quoted. etc.
* Can used Burp Repeater to test some candidate payloads and see the HTML response.

### Stored XSS

* XSS payload is stored on the web application and then gets run when other users visit the site or web page (persistent)
* any data that is stored in the web application can be tried to run javascript payload

{% hint style="info" %}
Suppose a website allows users to submit comments on blog posts, which are displayed to other users. Users submit comments using an HTTP request like the following:

`POST /post/comment HTTP/1.1 Host: vulnerable-website.com Content-Length: 100 postId=3&comment=This+post+was+extremely+helpful.&name=Carlos+Montoya&email=carlos%40normal-user.net`

After this comment has been submitted, any user who visits the blog post will receive the following within the application's response:

`<p>This post was extremely helpful.</p>`

Assuming the application doesn't perform any other processing of the data, an attacker can submit a malicious comment like this:

`<script>/* Bad stuff here... */</script>`

Within the attacker's request, this comment would be URL-encoded as:

`comment=%3Cscript%3E%2F*%2BBad%2Bstuff%2Bhere...%2B*%2F%3C%2Fscript%3E`

Any user who visits the blog post will now receive the following within the application's response:

`<p><script>/* Bad stuff here... */</script></p>`

The script supplied by the attacker will then execute in the victim user's browser, in the context of their session with the application.
{% endhint %}

#### Methodology

* **Test every endpoint.**&#x20;
  * Parameters or other data within URL query string and message body
* **Find links between entry and exit points.**&#x20;
* **Systematically work through all data entry points, submitting a specific value into each one and monitoring the application's responses to detect cases where the submitted value appears.**

### DOM Based XSS

* Manipulating the way the JS interacts with the DOM. Typically only run on the client side.
* Document Object model ⇒ programming interface for HTML documents
* JS execution happens directly in the browser without new pages being loaded
* any variable that an attacker can have control over can be leveraged
  * e.g used in Juice Box `<iframe src="javascript:alert('xss')">`

{% hint style="info" %}
A site serves a static search page that uses client-side JavaScript to read the search term from the URL **fragment** (everything after `#`) and inject it into the page:

```html
<!-- /search.html -->
<div id="results"></div>

<script>
  // Client-side only: server never sees the fragment
  const term = decodeURIComponent(location.hash.slice(1) || "");
  // VULN: writes untrusted data as HTML
  document.getElementById("results").innerHTML =
    "You searched for: " + term;
</script>
```

A normal URL like this:

```
https://insecure-website.com/search.html#gift
```

produces this DOM at runtime:

```html
<div id="results">You searched for: gift</div>
```

Because the page uses `innerHTML` on attacker-controlled data, an attacker can craft:

```
https://insecure-website.com/search.html#%3Cimg%20src%3Dx%20onerror%3Dalert(1)%3E
```

(That fragment decodes to `<img src=x onerror=alert(1)>`.)

When a victim opens the URL, the browser executes the script via the `onerror` handler. The server’s response is unchanged (it’s the same static HTML), but the **DOM is modified client-side** and the payload runs in the victim’s session
{% endhint %}

### Blind XSS:

* payload gets stored on website for another user to view
* to execute, need a callback (usually an HTTP request) to make sure payload is run, you can use XSS hunter express
* To escape input tags we can use `">` which closes the value parameter and then closes the input tag
* to escape text area, same idea ⇒ `</textarea>`
* to escape Js code, same idea close the line with `';`
* to escape filter you can add extra characters that the filter will remove because it goes character by character
* polygots can to escape attributes, tags and bypass filters
