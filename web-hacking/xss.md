# XSS

Cross-site scripting, injection attacker where malicious Javascript gets injected into a web app

Types of payloads:

* Proof of concept: show that you achieved CSS on a website, usually with `alert`
* Session Stealing: details of user session ⇒ login tokens, kept in cookies, take over the session and use to login
* Key Logger: anything typed gets sent to a website used by hacker
* Business Logic: calling a particular network or JS function

Reflected XSS:

* user-supplied data in an HTTP request is included in webpage source without validation
  * attacker could send links or embed them into iframe containing JS payload

Stored XSS:

* XSS payload is stored on the web application and then gets run when other users visit the site or web page, typical point of entry is:
  * comments on a blog
  * user profile info
  * website listing
  * any data that is stored in the web application can be tried to run javascript payload

DOM Based XSS:

* Document Object model ⇒ programming interface for HTML documents
* JS execution happens directly in the browser without nerw pages being laooded
* any variable that an attacker can have control over can be leveraged

Blind XSS:

* payload gets stored on website for another user to view
* to executel, need a callback (usually an HTTP request) to make sure payload is run, you can use XSS hunter express
* To escape input tags we can use `">` which closes the value parameter and then closes the input tag
* to escape text area, same idea ⇒ `</textarea>`
* to escape Js code, same idea close the line with `';`
* to escape filter you can add extra characters that the filter will remove because it goes character by character
* polygots can to escape attributes, tags and bypass filters
