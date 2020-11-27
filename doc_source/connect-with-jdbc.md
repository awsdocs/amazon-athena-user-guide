# Using Athena with the JDBC Driver<a name="connect-with-jdbc"></a>

You can use a JDBC connection to connect Athena to business intelligence tools and other applications, such as [SQL Workbench](http://www.sql-workbench.eu/downloads.html)\. To do this, download, install, and configure the Athena JDBC driver, using the following links on Amazon S3\.

## Links for Downloading the JDBC Driver<a name="download-the-jdbc-driver"></a>

The JDBC driver version 2\.0\.16 complies with the JDBC API 4\.1 and 4\.2 data standards\. Before downloading the driver, check which version of Java Runtime Environment \(JRE\) you use\. The JRE version depends on the version of the JDBC API you are using with the driver\. If you are not sure, download the latest version of the driver\. 

Download the driver that matches your version of the JDK and the JDBC data standards:
+ The [AthenaJDBC41\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/AthenaJDBC41.jar) is compatible with JDBC 4\.1 and requires JDK 7\.0 or later\.
+ The [AthenaJDBC42\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/AthenaJDBC42.jar) is compatible with JDBC 4\.2 and requires JDK 8\.0 or later\.

### JDBC Driver Release Notes, License Agreement, and Notices<a name="atelong-jdbc-driver-license-agreement"></a>

After you download the version you need, read the release notes, and review the License Agreement and Notices\. 
+ [Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/release-notes.txt)
+ [License Agreement](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/LICENSE.txt)
+ [Notices](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/NOTICES.txt)
+ [Third\-Party Licenses](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/third-party-licenses.txt)

### JDBC Driver Documentation<a name="documentation-jdbc"></a>

Download the following documentation for the driver:
+ [JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf)\. Use this guide to install and configure the driver\.
+ [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. Use this guide to migrate from previous versions to the current version\.

**Important**  
 To use the JDBC driver for multiple data catalogs with Athena \(for example, when using an [external Hive metastore](connect-to-data-source-hive.md) or [federated queries](connect-to-a-data-source.md)\), include `MetadataRetrievalMethod=ProxyAPI` in your JDBC connection string\.

## Migration from Previous Version of the JDBC Driver<a name="migration-from-previous-jdbc-driver"></a>

The current JDBC driver version 2\.0\.16 is a drop\-in replacement of the previous version of the JDBC driver version 2\.0\.9, and is backwards compatible with the JDBC driver version 2\.0\.9, with the following step that you must perform to ensure the driver runs\. 

**Important**  
To use JDBC driver version 2\.0\.5 or later, attach a permissions policy to IAM principals using the JDBC driver that allows the `athena:GetQueryResultsStream` policy action\. This policy action is not exposed directly with the API\. It is only used with the JDBC driver as part of streaming results support\. For an example policy, see [AWSQuicksightAthenaAccess Managed Policy](awsquicksightathenaaccess-managed-policy.md)\.   
Additionally, ensure that port 444, which Athena uses to stream query results, is open to outbound traffic\. When you use a PrivateLink endpoint to connect to Athena, ensure that the security group attached to the PrivateLink endpoint is open to inbound traffic on port 444\. If port 444 is blocked, you may receive the error message \[Simba\]\[AthenaJDBC\]\(100123\) An error has occurred\. Exception during column initialization\.   
For more information about upgrading to versions 2\.0\.5 or later from version 2\.0\.2, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. 

For more information about the previous versions of the JDBC driver, see [Using Earlier Version JDBC Drivers](connect-with-previous-jdbc.md)\.

If you are migrating from a 1\.x driver to a 2\.x driver, you must migrate your existing configurations to the new configuration\. We highly recommend that you migrate to driver version 2\.x\. For information, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.16.1000/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\.