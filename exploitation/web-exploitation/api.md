# API

#### Broken Object Level Authorization (BOLA)

An IDOR where the user uses the input functionality and gets access to the resources they are not authorized to access (lack of Authorization Tokens in headers)

#### Broken User Authentication (BUA)

An API endpoint allows an attacker to access a database or acquire a higher privilege than the existing one through lack of authorization logic (e.g. does not check password)

#### Excessive Data Exposure

When applications disclose more than desired information through an API response, can lead to extracting confidential information.

#### Lack of Resources & Rate Limiting

APIs do not enforce any restriction on the frequency of clients requesting resources or the files' size. Can be used to ensure non-availablity of service for an organization.

#### Broken Function Level Authorization

When a low privileged user gets access to confidential data by impersonating a high privileged user (Admin).

#### Methodology

* **API Recon.** Identify all the endpoints (what input data the API processes, the types of requests the API accepts.). Can also be done through API documentation even if not available publicly with certain endpoints. By crawling the application we can also uncover hidden API endpoints (sometimes in JS files too).
