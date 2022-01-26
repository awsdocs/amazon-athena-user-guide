# January 13, 2022<a name="release-note-2022-01-13"></a>

Published on 2022\-01\-13

Released the JDBC 2\.0\.27 and ODBC 1\.1\.15 drivers for Athena\.

The JDBC 2\.0\.27 driver includes the following changes:
+ The driver has been updated to retrieve external catalogs\. 
+ The extended driver version number is now included in the `user-agent` string as part of the Athena API call\.

The ODBC 1\.1\.15 driver includes the following changes:
+ Corrects an issue with second calls to `SQLParamData()`\.

For more information about these changes, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC Driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.