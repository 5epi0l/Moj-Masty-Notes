
Second-Order SQL injection occurs when the application takes user input from an HTTP request and stores it for future use. This is done by placing the input into a database, but no vuln occurs at the point when the data is stored. Later, when handling a different HTTP request, the application retrieves the stored data and incorporated it into a SQL query in an unsafe way.



