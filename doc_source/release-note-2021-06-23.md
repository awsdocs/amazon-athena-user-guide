# June 23, 2021<a name="release-note-2021-06-23"></a>

Published on 2021\-06\-23

Released JDBC 2\.0\.23 and ODBC 1\.1\.10 drivers for Athena\. Both drivers offer improved read performance and support [EXPLAIN](athena-explain-statement.md) statements and [parameterized queries](querying-with-prepared-statements.md)\. 

`EXPLAIN` statements show the logical or distributed execution plan of a SQL query\. Parameterized queries enable the same query to be used multiple times with different values supplied at run time\.

The JDBC release also adds support for Active Directory Federation Services 2019 and a custom endpoint override option for AWS STS\. The ODBC release fixes an issue with IAM profile credentials\. 

For more information and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.