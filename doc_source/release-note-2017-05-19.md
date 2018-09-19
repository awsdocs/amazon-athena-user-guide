# May 19, 2017<a name="release-note-2017-05-19"></a>

Published on *2017\-05\-19*

Added an Amazon Athena API and AWS CLI support for Athena; updated JDBC driver to version 1\.1\.0; fixed various issues\.
+  Amazon Athena enables application programming for Athena\. For more information, see [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. The latest AWS SDKs include support for the Athena API\. For links to documentation and downloads, see the *SDKs* section in [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\. 
+  The AWS CLI includes new commands for Athena\. For more information, see the [AWS CLI Reference for Athena](https://docs.aws.amazon.com/cli/latest/reference/athena/)\. 
+  A new JDBC driver 1\.1\.0 is available, which supports the new Athena API as well as the latest features and bug fixes\. Download the driver at [ https://s3\.amazonaws\.com/athena\-downloads/drivers/AthenaJDBC41\-1\.1\.0\.jar](https://s3.amazonaws.com/athena-downloads/drivers/AthenaJDBC41-1.1.0.jar)\. We recommend upgrading to the latest Athena JDBC driver; however, you may still use the earlier driver version\. Earlier driver versions do not support the Athena API\. For more information, see [Using Athena with the JDBC Driver](connect-with-jdbc.md)\.
+ Actions specific to policy statements in earlier versions of Athena have been deprecated\. If you upgrade to JDBC driver version 1\.1\.0 and have customer\-managed or inline IAM policies attached to JDBC users, you must update the IAM policies\. In contrast, earlier versions of the JDBC driver do not support the Athena API, so you can specify only deprecated actions in policies attached to earlier version JDBC users\. For this reason, you shouldn't need to update customer\-managed or inline IAM policies\.
+ These policy\-specific actions were used in Athena before the release of the Athena API\. Use these deprecated actions in policies **only** with JDBC drivers earlier than version 1\.1\.0\. If you are upgrading the JDBC driver, replace policy statements that allow or deny deprecated actions with the appropriate API actions as listed or errors will occur:


| Deprecated Policy\-Specific Action | Corresponding Athena API Action | 
| --- |--- |
|  <pre>athena:RunQuery</pre>  |  <pre>athena:StartQueryExecution</pre>  | 
|  <pre>athena:CancelQueryExecution</pre>  |  <pre>athena:StopQueryExecution</pre>  | 
|  <pre>athena:GetQueryExecutions</pre>  |  <pre>athena:ListQueryExecutions</pre>  | 

## Improvements<a name="release-note-2017-05-19-improvements"></a>
+ Increased the query string length limit to 256 KB\.

## Bug Fixes<a name="release-note-2017-05-19-bug-fixes"></a>
+ Fixed an issue that caused query results to look malformed when scrolling through results in the console\.
+  Fixed an issue where a `\u0000` character string in Amazon S3 data files would cause errors\. 
+ Fixed an issue that caused requests to cancel a query made through the JDBC driver to fail\.
+ Fixed an issue that caused the AWS CloudTrail SerDe to fail with Amazon S3 data in US East \(Ohio\)\.
+ Fixed an issue that caused DROP TABLE to fail on a partitioned table\.