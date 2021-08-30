# March 05, 2019<a name="release-note-2019-03-05"></a>

Published on *2019\-03\-05*

Amazon Athena is now available in the Canada \(Central\) Region\. For a list of supported Regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\. Released the new version of the ODBC driver with support for Athena workgroups\. For more information, see the [ODBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/release-notes.txt)\.

To download the ODBC driver version 1\.0\.5 and its documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\. For information about this version, see the [ODBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/release-notes.txt)\. 

To use workgroups with the ODBC driver, set the new connection property, `Workgroup`, in the connection string as shown in the following example:

```
Driver=Simba Athena ODBC Driver;AwsRegion=[Region];S3OutputLocation=[S3Path];AuthenticationType=IAM Credentials;UID=[YourAccessKey];PWD=[YourSecretKey];Workgroup=[WorkgroupName]
```

For more information, search for "workgroup" in the [ODBC Driver Installation and Configuration Guide version 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf)\. There are no changes to the ODBC driver connection string when you use tags on workgroups\. To use tags, upgrade to the latest version of the ODBC driver, which is this current version\.

This driver version lets you use [Athena API workgroup actions](workgroups-api-list.md) to create and manage workgroups, and [Athena API tag actions](tags-operations.md) to add, list, or remove tags on workgroups\. Before you begin, make sure that you have resource\-level permissions in IAM for actions on workgroups and tags\. 

For more information, see:
+ [Using Workgroups for Running Queries](workgroups.md) and [Workgroup Example Policies](example-policies-workgroup.md)\.
+ [Tagging Athena Resources](tags.md) and [Tag\-Based IAM Access Control Policies](tags-access-control.md)\.

If you use the JDBC driver or the AWS SDK, upgrade to the latest version of the driver and SDK, both of which already include support for workgroups and tags in Athena\. For more information, see [Using Athena with the JDBC Driver](connect-with-jdbc.md)\.