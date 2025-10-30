# Burp Suite

#### Intruder

automated request manipulation and enables tasks such as fuzzing (⇒process of testing functionality or existence by applying a set of data to a parameter) and brute-forcing

* **Positions:** select attack type
  * positions highlighted in green and enclosed by `§`
* **Payloads:** values to insert into positions defined, allow for suffix/prefix, match and replace…
* **Resource Pool:** resource allocation for Burp Professional
* **Settings:** how burp handles results and the attacks itself
* if a session cookie is set (meaning a same session cannot be used twice) and changed every time the website is refreshed we need to use **Burp Macros**

Attack Types:

* Sniper: cycles through payloads, inserting one payload at a time into each position ⇒ linear
* Battering Ram: sends all payloads simultaneously, each payload inserted into its respective position ⇒ good for race conditions
* Pitchfork: simultaneous testing of multiple positions with different payloads ⇒ define multiple payload sets each associated with a specific position ⇒ good for testing parameters that need seperate testing
* Cluster Bomb: combines Sniper and Pitchfork

#### Decoder and Comparer and Organizer (annotating) both do exactly what you think

#### Sequencer:

evaluate entropy or randomness of tokens (⇒ strings used to identify something and should be generated in a cryptographically secure manner), if not generated securely we could predict upcoming token values

* Live Capture: pass a request that will generate a token to sequencer for analysis
* Manual Load: list of pre-generated tokens samples directly into Sequencer
