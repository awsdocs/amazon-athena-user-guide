# April 13, 2022<a name="release-note-2022-04-13"></a>

Published on 2022\-04\-13

Athena releases JDBC driver version 2\.0\.28\. The JDBC 2\.0\.28 driver includes the following changes:
+ **JWT support** – The driver now supports JSON web tokens \(JWT\) for authentication\. For information about using JWT with the JDBC driver, see the installation and configuration guide, downloadable from the [JDBC driver page](connect-with-jdbc.md)\.
+ **Updated Log4j libraries** – The JDBC driver now uses the following Log4j libraries:
  + Log4j\-api 2\.17\.1 \(previously 2\.17\.0\)
  + Log4j\-core 2\.17\.1 \(previously 2\.17\.0\) 
  + Log4j\-jcl 2\.17\.2
+ **Other improvements** – The new driver also includes the following improvements and bug fixes:
  + The Athena prepared statements feature is now available through JDBC\. For information about prepared statements, see [Querying with prepared statements](querying-with-prepared-statements.md)\.
  + Athena JDBC SAML federation is now functional for the China Regions\.
  + Additional minor improvements\.

For more information, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.