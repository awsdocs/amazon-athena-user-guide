# October 29, 2021<a name="release-note-2021-10-29"></a>

Published on 2021\-10\-29

Athena releases JDBC 2\.0\.25 and ODBC 1\.1\.13 drivers and announces features and improvements\.

## JDBC and ODBC Drivers<a name="release-note-2021-10-29-jdbc-odbc"></a>

Released JDBC 2\.0\.25 and ODBC 1\.1\.13 drivers for Athena\. Both drivers offer support for browser SAML multi\-factor authentication, which can be configured to work with any SAML 2\.0 provider\.

The JDBC 2\.0\.25 driver includes the following changes:
+ Support for browser SAML authentication\. The driver includes a browser SAML plugin which can be configured to work with any SAML 2\.0 provider\.
+ Support for AWS Glue API calls\. You can use the `GlueEndpointOverride` parameter to override the AWS Glue endpoint\. 
+ Changed the `com.simba.athena.amazonaws` class path to `com.amazonaws`\.

The ODBC 1\.1\.13 driver includes the following changes:
+ Support for browser SAML authentication\. The driver includes a browser SAML plugin which can be configured to work with any SAML 2\.0 provider\. For an example of how to use the browser SAML plugin with the ODBC driver, see [Configuring Single Sign\-On Using ODBC, SAML 2\.0, and the Okta Identity Provider](okta-saml-sso.md)\.
+ You can now configure the role session duration when you use ADFS, Azure AD, or Browser Azure AD for authentication\.

For more information about these and other changes, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC Driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

## Features and Improvements<a name="release-note-2021-10-29-features-improvements"></a>

Athena announces the following features and improvements\.
+ A new optimization rule has been introduced to avoid duplicate table scans in certain cases\.
+ Fixed the LambdaFunctionException: class not found bug in the [Athena JDBC federated connector](athena-prebuilt-data-connectors-jdbc.md)\.