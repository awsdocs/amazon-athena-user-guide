# Access through JDBC and ODBC Connections<a name="policy-actions"></a>

To gain access to AWS services and resources, such as Athena and the Amazon S3 buckets, provide the JDBC or ODBC driver credentials to your application\. If you are using the JDBC or ODBC driver, ensure that the IAM permissions policy includes all of the actions listed in [AWSQuicksightAthenaAccess Managed Policy](awsquicksightathenaaccess-managed-policy.md)\.

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

For information about the latest versions of the JDBC and ODBC drivers and their documentation, see [Using Athena with the JDBC Driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.