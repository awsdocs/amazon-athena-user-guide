# November 20, 2018<a name="release-note-2018-11-20"></a>

Published on *2018\-11\-20*

Released the new versions of the JDBC and ODBC driver with support for federated access to Athena API with the AD FS and SAML 2\.0 \(Security Assertion Markup Language 2\.0\)\. For details, see the [JDBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.6/docs/release-notes.txt) and [ODBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/release-notes.txt)\.

With this release, federated access to Athena is supported for the Active Directory Federation Service \(AD FS 3\.0\)\. Access is established through the versions of JDBC or ODBC drivers that support SAML 2\.0\. For information about configuring federated access to the Athena API, see [Enabling Federated Access to the Athena API](access-federation-saml.md)\.

To download the JDBC driver version 2\.0\.6 and its documentation, see [Using Athena with the JDBC Driver](connect-with-jdbc.md)\. For information about this version, see [JDBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.6/docs/release-notes.txt)\.

To download the ODBC driver version 1\.0\.4 and its documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\. For information about this version, [ODBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/release-notes.txt)\. 

For more information about SAML 2\.0 support in AWS, see [About SAML 2\.0 Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html) in the *IAM User Guide*\. 