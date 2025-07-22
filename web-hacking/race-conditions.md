# Race Conditions

race condition is a situation where the timing of events influences the behavior and outcome of the program ⇒ usually when a variable gets accessed and modified by multiple threads, and due to lack of proper lock mechanisms and sync between threads attackers can leverage

* A thread is a lightweight unit of execution, it shares various memory parts and instructions with the process (**difference between software threads and hardware threads, software threads are in the 1000s while hardware is anywhere from 16-32+, OS handles software thread execution/CPU time to use the more limited hardware threads)**
  * Serial: one process is running, it servers one user after the other sequentially, new users are enqueued
  * Parallel: one process is running, it creates a thread to server every new user, new users are only enqueued after the maximum number of running threads is reached
* Common cause of race conditions is shared resources, when multiple threads concurrently access and modify data
* Parallel Execution: web server execute multiple request in parallel to handle concurrent user interactions, if they access resources shared without sync ⇒ race conditions
* Database Operations: concurrent database operation, such as read-modify-write sequences can introduce race conditions
* 3rd party library and services: if these 3rd party don’t handle concurrent access properly, race conditions introduced

Typical Web Application Architecture:

* presentation tier: in web apps, consists of browser on the client side ⇒ JS, HTML, CSS
* application tier: business logic and functionality, receives client requests, processes them, and interacts with the data tier ⇒ node.js, php
* data tier: sotring and manipulating the app data ⇒ SQL
* must leverage the “window of opportunity” in which no check is made to accept something repeatedly ⇒ requires good timing so that requests reach server simultaneaously]

How to do with Burp:

1. Explore/Study how web app received HTTP Requests (view HTTP history tab)
2. Find POST request with transaction (or whatever race condition)
3. Send to Repeater (right click)
4. Create tab group for request, rick clight on the tab and duplicate x times
5. Sending Request group
   1. in sequence:
      1. single connection: useful for potential client-side desync vulnerabilities
      2. separate connections: opens TCP connection, sends request from the group, closes TCP connection, and repeats for all requests
   2. in parallel: send all requests at once, when sending in parallel, repeater uses different techniques to sync the requests’ arrival at the target depending on HTTP protocol being used
