# August 16, 2018<a name="release-note-2018-08-16"></a>

Published on *2018\-08\-16*

Released the JDBC driver version 2\.0\.5\. The new version of the JDBC driver streams results by default, instead of paging through them, allowing business intelligence tools to retrieve large data sets faster\. Compared to the previous version of the JDBC driver, there are the following performance improvements:
+ Approximately 2x performance increase when fetching less than 10K rows\.
+ Approximately 5\-6x performance increase when fetching more than 10K rows\. 

The streaming results feature is available only with the JDBC driver\. It is not available with the ODBC driver\. You cannot use it with the Athena API\. For information about streaming results, see the [ JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf), and search for **UseResultsetStreaming**\.

For downloading the JDBC driver version 2\.0\.5 and its documentation, see [Using Athena with the JDBC Driver](connect-with-jdbc.md)\. 

The JDBC driver version 2\.0\.5 is a drop\-in replacement for the previous version of the driver \(2\.0\.2\)\. To ensure that you can use the JDBC driver version 2\.0\.5, add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API and is only used with the JDBC driver, as part of streaming results support\. For an example policy, see [AWSQuicksightAthenaAccess Managed Policy](awsquicksightathenaaccess-managed-policy.md)\. For more information about migrating from version 2\.0\.2 to version 2\.0\.5 of the driver, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. 

If you are migrating from a 1\.x driver to a 2\.x driver, you will need to migrate your existing configurations to the new configuration\. We highly recommend that you migrate to the current version of the driver\. For more information, see [Using the Previous Version of the JDBC Driver](connect-with-previous-jdbc.md), and the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. 