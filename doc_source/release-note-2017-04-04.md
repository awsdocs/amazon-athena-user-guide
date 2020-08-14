# April 4, 2017<a name="release-note-2017-04-04"></a>

Published on *2017\-04\-04*

Added support for Amazon S3 data encryption and released JDBC driver update \(version 1\.0\.1\) with encryption support, improvements, and bug fixes\.

## Features<a name="release-note-2017-04-04-features"></a>
+ Added the following encryption features:
  +  Support for querying encrypted data in Amazon S3\. 
  +  Support for encrypting Athena query results\. 
+ A new version of the driver supports new encryption features, adds improvements, and fixes issues\.
+ Added the ability to add, replace, and change columns using `ALTER TABLE`\. For more information, see [Alter Column](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterColumn) in the Hive documentation\.
+ Added support for querying LZO\-compressed data\.

For more information, see [Encryption at Rest](encryption.md)\.

## Improvements<a name="release-note-2017-04-04-improvements"></a>
+ Better JDBC query performance with page\-size improvements, returning 1,000 rows instead of 100\.
+ Added ability to cancel a query using the JDBC driver interface\.
+ Added ability to specify JDBC options in the JDBC connection URL\. For more information, see [Using Athena with the Previous Version of the JDBC Driver](connect-with-previous-jdbc.md) for the previous version of the driver, and [Connect with the JDBC](connect-with-jdbc.md), for the most current version\.
+ Added PROXY setting in the driver, which can now be set using [ClientConfiguration](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/ClientConfiguration.html) in the AWS SDK for Java\.

## Bug Fixes<a name="release-note-2017-04-04-bug-fixes"></a>

Fixed the following bugs:
+ Throttling errors would occur when multiple queries were issued using the JDBC driver interface\.
+ The JDBC driver would stop when projecting a decimal data type\.
+  The JDBC driver would return every data type as a string, regardless of how the data type was defined in the table\. For example, selecting a column defined as an `INT` data type using `resultSet.GetObject()` would return a `STRING` data type instead of `INT`\. 
+ The JDBC driver would verify credentials at the time a connection was made, rather than at the time a query would run\.
+ Queries made through the JDBC driver would fail when a schema was specified along with the URL\.