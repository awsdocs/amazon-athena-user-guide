# Access through JDBC and ODBC connections<a name="policy-actions"></a>

To gain access to AWS services and resources, such as Athena and the Amazon S3 buckets, provide the JDBC or ODBC driver credentials to your application\. If you are using the JDBC or ODBC driver, ensure that the IAM permissions policy includes all of the actions listed in [AWSQuicksightAthenaAccess Managed Policy](managed-policies.md#awsquicksightathenaaccess-managed-policy)\.

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

## Authentication methods<a name="security-jdbc-odbc-access-authentication"></a>

The Athena JDBC and ODBC drivers support SAML 2\.0\-based authentication, including the following identity providers:
+ Active Directory Federation Services \(AD FS\)
+ Azure Active Directory \(AD\)
+ Okta 
+ PingFederate

For more information, see the installation and configuration guides for the respective drivers, downloable in PDF format from the [JDBC](connect-with-jdbc.md) and [ODBC](connect-with-odbc.md) driver pages\. For additional related information, see the following:
+ [Enabling federated access to the Athena API](access-federation-saml.md)
+ [Using Lake Formation and the Athena JDBC and ODBC drivers for federated access to Athena](security-athena-lake-formation-jdbc.md)
+  [Configuring single sign\-on using ODBC, SAML 2\.0, and the Okta Identity Provider](okta-saml-sso.md)

For information about the latest versions of the JDBC and ODBC drivers and their documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.