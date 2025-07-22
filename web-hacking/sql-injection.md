# SQL Injection

user-provided data gets included in the SQL query

* `;--` ⇒ end of the SQL statement and the two dashes cause everything afterwards to be treated as a comment
* In-Band: the same method of communicating being used to exploit the vulnerability and also receive results
* Error-Based: most useful for obtaining info about database structure, error messages from the database are printed directly to the browser screen ⇒ enumerate whole database
  * use certain characters until an error message is produced ⇒ single aprostrophes or quotation marks
* Union-Based: utilises the SQL UNION operator with SELECT to return additional results to the page
  * UNION statement combines two or more SELECT statements to retrieve data from either single or multiple tables ⇒ must retrieve the same number of columns in each SELECT statement, similar data type and column order has to be the same
  * `SELECT name,address,city,postcode from customers UNION SELECT company,address,city,postcode from suppliers;`
* Blind: can’t know if injected queries were successful, error messages disabled
  * authentication bypass: login forms that are connected to a database are developed to see if there is matching pair in the users table (is there a user with the username bob and the password bob123?)
    * we don’t need to enumerate, just create a database query that replies with a yes/true
  * Boolean Based: response we receive from our injection attemps ⇒ true/false, confirms that our payload was successful or not
    * goal is to enumerate the table to find all of its contents
  * Time-Based: instead of a boolean we check the time it takes for a query to complete, `SLEEP (x)` will only get executed upon a successful UNION SELECT
    * app will hang
* Out-of-Band: depends on features enabled on database server or business logic, 2 communication channels, attack channel is a web request and the data gathering could be monitoring HTTP/DNS requests of service I control
