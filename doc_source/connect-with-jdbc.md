# Using Athena with the JDBC Driver<a name="connect-with-jdbc"></a>

You can use a JDBC connection to connect Athena to business intelligence tools, such as SQL Workbench\. To do this, download, install, and configure the Athena JDBC driver, using the following link on Amazon S3\.

## Migration from Previous Version of the JDBC Driver<a name="migration-from-previous-jdbc-driver"></a>

The current JDBC driver version 2\.x is *not* a drop\-in replacement of the previous version of the JDBC driver, and is *not* backwards compatible with the JDBC driver version 1\.x that you used before\. 

**Important**  
The latest version of the JDBC driver is 2\.0\.2\. If you are migrating from a 1\.x driver to a 2\.x driver, you will need to migrate your existing configurations to the new configuration\. We highly recommend that you migrate to the current driver\.   
For information about the changes introduced in the new version of the driver, the version differences, and examples, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\.   
For information about the previous version of the JDBC driver, see [Using Athena with the Previous Version of the JDBC Driver](connect-with-previous-jdbc.md)\.

## Links for Downloading the JDBC Driver<a name="download-the-jdbc-driver"></a>

The JDBC driver complies with the JDBC API 4\.1 and 4\.2 data standards\. 

**Note**  
The JDBC driver version 2\.x is not available for JDK 6\.0 \(Java 1\.6\), and is not compatible with JDBC API version 4\.0\. 

Before downloading the driver, check which version of Java Runtime Environment \(JRE\) you use\. The JRE version depends on the version of the JDBC API you are using with the driver\. If you are not sure, download the latest version of the driver\. 

Download the driver that matches your version of the JDK and the JDBC data standards\.
+ The [AthenaJDBC41\-2\.0\.2\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/AthenaJDBC41_2.0.2.jar) is compatible with JDBC 4\.1 and requires JDK 7\.0 or later\.
+ The [AthenaJDBC42\-2\.0\.2\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/AthenaJDBC42_2.0.2.jar) is compatible with JDBC 4\.2 and requires JDK 8\.0 or later\.

## JDBC Driver Release Notes, License Agreement, and Notices<a name="atelong-jdbc-driver-license-agreement"></a>

After you download the version you need, read the release notes, and review the License Agreement and Notices\. 
+ [Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/release-notes.txt)
+ [License Agreement](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/LICENSE.txt)
+ [Notices](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/NOTICES.txt)

Now you are ready to migrate from the previous version and install and configure this version of the JDBC driver\. 

## JDBC Driver Documentation<a name="documentation-jdbc"></a>

To install and configure the JDBC driver version 2\.x, see the [JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf)\.

To migrate from the previous version of the JDBC driver to this driver, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\.