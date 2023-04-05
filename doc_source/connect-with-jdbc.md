# Connecting to Amazon Athena with JDBC<a name="connect-with-jdbc"></a>

You can use a JDBC connection to connect Athena to business intelligence tools and other applications, such as [SQL workbench](http://www.sql-workbench.eu/downloads.html)\. To do this, use the Amazon S3 links on this page to download, install, and configure the Athena JDBC driver\. For information about building the JDBC connection URL, see the downloadable [JDBC driver installation and configuration guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/docs/Simba+Amazon+Athena+JDBC+Connector+Install+and+Configuration+Guide.pdf)\. For permissions information, see [Access through JDBC and ODBC connections](policy-actions.md)\. To submit feedback regarding the JDBC driver, email [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\. Starting with version 2\.0\.24, two versions of the driver are available: one that includes the AWS SDK, and one that does not\.

**Important**  
When you use the JDBC driver, be sure to note the following requirements:  
**Open port 444** – Keep port 444, which Athena uses to stream query results, open to outbound traffic\. When you use a PrivateLink endpoint to connect to Athena, ensure that the security group attached to the PrivateLink endpoint is open to inbound traffic on port 444\. If port 444 is blocked, you may receive the error message \[Simba\]\[AthenaJDBC\]\(100123\) An error has occurred\. Exception during column initialization\. 
**athena:GetQueryResultsStream policy** – Add the `athena:GetQueryResultsStream` policy action to the IAM principals that use the JDBC driver\. This policy action is not exposed directly with the API\. It is used only with the ODBC and JDBC drivers as part of streaming results support\. For an example policy, see [AWS managed policy: AWSQuicksightAthenaAccess](managed-policies.md#awsquicksightathenaaccess-managed-policy)\. 
**Using the JDBC driver for multiple data catalogs** – To use the JDBC driver for multiple data catalogs with Athena \(for example, when using an [external Hive metastore](connect-to-data-source-hive.md) or [federated queries](connect-to-a-data-source.md)\), include `MetadataRetrievalMethod=ProxyAPI` in your JDBC connection string\. 
**4\.1 drivers** – Starting in 2023, driver support for JDBC version 4\.1 is discontinued\. No further updates will be released\. If you are using a JDBC 4\.1 driver, migration to the 4\.2 driver is highly recommended\. 

## JDBC driver with AWS SDK<a name="download-the-jdbc-driver"></a>

The JDBC driver version 2\.0\.35 complies with the JDBC API 4\.2 data standard and requires JDK 8\.0 or later\. For information about checking the version of Java Runtime Environment \(JRE\) that you use, see the Java [documentation](https://www.java.com/en/download/help/version_manual.html)\.

Use the following link to download the JDBC 4\.2 driver `.jar` file\.
+ [AthenaJDBC42\-2\.0\.35\.1000\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/AthenaJDBC42-2.0.35.1000.jar)

The following `.zip` file download contains the `.jar` file for JDBC 4\.2 and includes the AWS SDK and the accompanying documentation, release notes, licenses, and agreements\.
+ [SimbaAthenaJDBC\-2\.0\.35\.1000\.zip](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/SimbaAthenaJDBC-2.0.35.1000.zip)

## JDBC driver without AWS SDK<a name="download-the-jdbc-driver-no-sdk"></a>

The JDBC driver version 2\.0\.35 complies with the JDBC API 4\.2 data standard and requires JDK 8\.0 or later\. For information about checking the version of Java Runtime Environment \(JRE\) that you use, see the Java [documentation](https://www.java.com/en/download/help/version_manual.html)\.

Use the following link to download the JDBC 4\.2 driver `.jar` file without the AWS SDK\.
+ [AthenaJDBC42\-2\.0\.35\.1001\.jar](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1001/AthenaJDBC42-2.0.35.1001.jar)

The following `.zip` file download contains the `.jar` file for JDBC 4\.2 and the accompanying documentation, release notes, licenses, and agreements\. It does not include the AWS SDK\.
+ [SimbaAthenaJDBC\-2\.0\.35\.1001\.zip](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1001/SimbaAthenaJDBC-2.0.35.1001.zip)

## JDBC driver release notes, license agreement, and notices<a name="atelong-jdbc-driver-license-agreement"></a>

After you download the version you need, read the release notes, and review the License Agreement and Notices\. 
+ [Release notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/docs/release-notes.txt)
+ [License agreement](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/docs/LICENSE.txt)
+ [Notices](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/docs/NOTICES.txt)
+ [Third\-party licenses](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/docs/third-party-licenses.txt)

## JDBC driver documentation<a name="documentation-jdbc"></a>

Download the following documentation for the driver:
+ [JDBC driver installation and configuration guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/docs/Simba+Amazon+Athena+JDBC+Connector+Install+and+Configuration+Guide.pdf)\. Use this guide to install and configure the driver\.
+ [JDBC driver migration guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.35.1000/docs/Simba+Amazon+Athena+JDBC+Connector+Migration+Guide.pdf)\. Use this guide to migrate from previous versions to the current version\.