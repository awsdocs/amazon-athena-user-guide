# Using Athena with the JDBC Driver<a name="connect-with-jdbc"></a>

You can use a JDBC connection to connect Athena to business intelligence tools and other applications, such as [SQL Workbench](http://www.sql-workbench.eu/downloads.html)\. To do this, download, install, and configure the Athena JDBC driver, using the following links on Amazon S3\.

## Links for Downloading the JDBC Driver<a name="download-the-jdbc-driver"></a>

The JDBC driver version 2\.0\.5 complies with the JDBC API 4\.1 and 4\.2 data standards\. Before downloading the driver, check which version of Java Runtime Environment \(JRE\) you use\. The JRE version depends on the version of the JDBC API you are using with the driver\. If you are not sure, download the latest version of the driver\. 

Download the driver that matches your version of the JDK and the JDBC data standards:
+ The [AthenaJDBC41\-2\.0\.5\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/AthenaJDBC41_2.0.5.jar) is compatible with JDBC 4\.1 and requires JDK 7\.0 or later\.
+ The [AthenaJDBC42\-2\.0\.5\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/AthenaJDBC42_2.0.5.jar) is compatible with JDBC 4\.2 and requires JDK 8\.0 or later\.

## JDBC Driver Release Notes, License Agreement, and Notices<a name="atelong-jdbc-driver-license-agreement"></a>

After you download the version you need, read the release notes, and review the License Agreement and Notices\. 
+ [Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/release-notes.txt)
+ [License Agreement](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/LICENSE.txt)
+ [Notices](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/NOTICES.txt)

Now you are ready to migrate from the previous version and install and configure this version of the JDBC driver\. 

### JDBC Driver Documentation<a name="documentation-jdbc"></a>

Download the following documentation for the driver:
+ [ JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf)\. Use this guide to install and configure the driver\.
+ [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. Use this guide to migrate from previous versions to the current version\.

## Migration from Previous Version of the JDBC Driver<a name="migration-from-previous-jdbc-driver"></a>

The current JDBC driver version 2\.0\.5 is a drop\-in replacement of the previous version of the JDBC driver version 2\.0\.2, and is backwards compatible with the JDBC driver version 2\.0\.2, with the following step that you must perform to ensure the driver runs\. 

**Important**  
To ensure that you can use the JDBC driver version 2\.0\.5, add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API and is only used with the JDBC driver, as part of streaming results support\. For an example policy, see [AWSQuicksightAthenaAccess Managed Policy](access.md#awsquicksightathenaaccess-managed-policy)\. For more information about upgrading to version 2\.0\.5 from version 2\.0\.2, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\.

For more information about the previous versions of the JDBC driver, see [Using the Previous Version of the JDBC Driver](connect-with-previous-jdbc.md)\.

If you are migrating from a 1\.x driver to a 2\.x driver, you will need to migrate your existing configurations to the new configuration\. We highly recommend that you migrate to the driver version 2\.x\. For information, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. 