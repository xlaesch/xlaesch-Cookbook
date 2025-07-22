# Command Injection

abuse of app’s behavior to execute commands on the operating system, using the same privileges that the application on a device is running with also known as Remote Code Execution (RCE)

* possible when programming languages pass data to and to make system calls on the machine’s operating system
* applications that use user input to populate system commands with data can be leveraged
* blind command injection: no direct output from the application when testing payloads, will have to investigate app behavior to determine if successful
  * use payloads that cause delay like `ping` or `sleep` the app will hang
  * force some output, using redirection operators such as `>`
  * `curl`
* verbose command injection: direct feedback once you have tested payload

Mitigations:

* avoid using vulnerable functions: `exec` `passthru` `system`
* input sanitisation: remove numerical data or special characters
* [cheat sheet](https://github.com/payloadbox/command-injection-payload-list)
