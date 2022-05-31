# Using Athena with the JDBC driver<a name="connect-with-jdbc"></a>

You can use a JDBC connection to connect Athena to business intelligence tools and other applications, such as [SQL workbench](http://www.sql-workbench.eu/downloads.html)\. To do this, use the Amazon S3 links on this page to download, install, and configure the Athena JDBC driver\. For permissions information, see [Access through JDBC and ODBC connections](policy-actions.md)\. Starting with version 2\.0\.24, two versions of the driver are available: one that includes the AWS SDK, and one that does not\.

## JDBC driver with AWS SDK<a name="download-the-jdbc-driver"></a>

The JDBC driver version 2\.0\.30 complies with the JDBC 4\.2 data standard\.
+ The [AthenaJDBC42\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/AthenaJDBC42.jar) is compatible with JDBC 4\.2 and requires JDK 8\.0 or later\.

The following `.zip` file download contains the `.jar` files for JDBC 4\.2 and includes the AWS SDK\.
+ [SimbaAthenaJDBC\-2\.0\.30\.1000\.zip](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/SimbaAthenaJDBC-2.0.30.1000.zip)

## JDBC driver without AWS SDK<a name="download-the-jdbc-driver-no-sdk"></a>

The JDBC driver version 2\.0\.30 complies with the JDBC API 4\.2 data standard\.

The following file does not include the AWS SDK:
+ The [AthenaJDBC42\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1001/AthenaJDBC42.jar) is compatible with JDBC 4\.2 and requires JDK 8\.0 or later\.

The following `.zip` file download contains the `.jar` files for JDBC 4\.2\. It does not include the AWS SDK\.
+ [SimbaAthenaJDBC\-2\.0\.30\.1001\.zip](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1001/SimbaAthenaJDBC-2.0.30.1001.zip)

## JDBC driver release notes, license agreement, and notices<a name="atelong-jdbc-driver-license-agreement"></a>

After you download the version you need, read the release notes, and review the License Agreement and Notices\. 
+ [Release notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/docs/release-notes.txt)
+ [License agreement](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/docs/LICENSE.txt)
+ [Notices](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/docs/NOTICES.txt)
+ [Third\-party licenses](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/docs/third-party-licenses.txt)

## JDBC driver documentation<a name="documentation-jdbc"></a>

Download the following documentation for the driver:
+ [JDBC driver installation and configuration guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/docs/Simba+Amazon+Athena+JDBC+Connector+Install+and+Configuration+Guide.pdf)\. Use this guide to install and configure the driver\.
+ [JDBC driver migration guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1000/docs/Simba+Amazon+Athena+JDBC+Connector+Migration+Guide.pdf)\. Use this guide to migrate from previous versions to the current version\.

**Important**  
 To use the JDBC driver for multiple data catalogs with Athena \(for example, when using an [external Hive metastore](connect-to-data-source-hive.md) or [federated queries](connect-to-a-data-source.md)\), include `MetadataRetrievalMethod=ProxyAPI` in your JDBC connection string\.

## Migration from previous version of the JDBC driver<a name="migration-from-previous-jdbc-driver"></a>

The current JDBC driver version 2\.0\.30 is a drop\-in replacement of the previous version of the JDBC driver, and is backwards compatible with previous 2\.x versions, with the following step that you must perform to ensure the driver runs\. 

**Important**  
To use JDBC driver version 2\.0\.5 or later, attach a permissions policy to IAM principals using the JDBC driver that allows the `athena:GetQueryResultsStream` policy action\. This policy action is not exposed directly with the API\. It is only used with the JDBC driver as part of streaming results support\. For an example policy, see [AWS managed policy: AWSQuicksightAthenaAccess](managed-policies.md#awsquicksightathenaaccess-managed-policy)\.   
Additionally, ensure that port 444, which Athena uses to stream query results, is open to outbound traffic\. When you use a PrivateLink endpoint to connect to Athena, ensure that the security group attached to the PrivateLink endpoint is open to inbound traffic on port 444\. If port 444 is blocked, you may receive the error message \[Simba\]\[AthenaJDBC\]\(100123\) An error has occurred\. Exception during column initialization\.   
For more information about upgrading to versions 2\.0\.5 or later from version 2\.0\.2, see the [JDBC driver migration guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1001/docs/Simba+Amazon+Athena+JDBC+Connector+Migration+Guide.pdf)\. 

For more information about the previous versions of the JDBC driver, see [Using earlier version JDBC drivers](connect-with-previous-jdbc.md)\.

If you are migrating from a 1\.x driver to a 2\.x driver, you must migrate your existing configurations to the new configuration\. We highly recommend that you migrate to driver version 2\.x\. For information, see the [JDBC driver migration guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.30.1001/docs/Simba+Amazon+Athena+JDBC+Connector+Migration+Guide.pdf)\.