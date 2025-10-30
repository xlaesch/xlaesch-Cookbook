# File Uploads

### File Upload Attack Methodology

1. Initial Recon
   * Analyze the site structure and technologies (e.g., Wappalyzer, Burp headers).
   * Identify upload functionality.
2. Client-Side Checks
   * Review source/JavaScript for upload restrictions.
   * Test by uploading a harmless file.
3. File Handling Discovery
   * Locate where/how uploaded files are stored (direct URL, embedded, renamed).
   * Use Gobuster (-x for extensions) to brute-force hidden locations.
4. Baseline Upload
   * Confirm accepted file behavior to use as a control sample.
5. Attempt Malicious Upload
   * Upload a payload, bypassing client-side filters.
   * Observe server error messages for clues.
6. Identify Server-Side Filtering
   * Extension filter: Try nonsense extensions → whitelist vs. blacklist.
   * Magic number filter: Modify file signature.
   * MIME filter: Change Content-Type in Burp.
   * File size filter: Upload progressively larger files.
7. Exploit Path
   * Use insights from filter tests to craft a valid bypass payload.
   * Deploy reverse shell/webshell if possible

#### Remote Code Execution

Usually achieved via low-privileged account (`www-data` in Linux) and with the same language as is being used for the backend of the website. We are looking to upload some sort of shell, either a webshell or reverse/bind shell.

#### Overwriting Existing Files

If file permissions are poorly set or no additional checks are made (very rare), you can upload a file and overwrite existing files on the disk.

### Filtering Types

#### Extension Validation

Extensions are used to identify the contents of a file. Filters that check for extensions either use blacklist extensions (extensions that are not allowed) or whitelist extensions.

#### File Type Filtering

More intensive than the aformentioned using:

* Multipurpose Internet Maild Extension (MIME) validation used as identifier for files. Transfered over mail and HTTP(S) protocols.
* Magic Number Validation is a string of bytes at the very beginning of the file content that allows you to identify the content. Usually done with Unix systems. Not possible to fake.

#### File Length Filtering

Prevent huge files form being uploaded.

#### File Name Filtering

Usually involves adding a random string of characters at the end of a filename to ensure no duplicate files and/or sanitizing the name to remove "bad characters" (e.g. null bytes).

#### File Content Filtering

Involves scanning the conents of a file before uploading.

### Bypassing Filtering

#### Client-Side Filtering

There are four easy ways to bypass this type of filtering:

1. Blocking Javascript completely bypasses the client-side filtering.
2. Intercept and modify the incoming page using Burp Suite to remove Javascript filter.
3. Intercept and modify the file upload.
4. Send the file directly to the upload point using a tool like `curl` and `POST` . `curl -X POST -F "submit:" -F ":@"`

#### Server-Side Filtering

**File Extensions**

* Some files have different file extensions that work as well. (e.g .php is the usual but .phar among others works as well
* Another example would be to use a filename like so `shell.jpg.php`where our filter only checks if .jpg exists rather than checking the extension.
* Using `hexeditor` on Kali can allow to change the magic numbers at the beginning of a file
