# Release notes<a name="release-notes"></a>

Describes Amazon Athena features, improvements, and bug fixes by release date\.

**Topics**
+ [2022](#release-notes-2022)
+ [2021](#release-notes-2021)
+ [2020](#release-notes-2020)
+ [2019](#release-notes-2019)
+ [2018](#release-notes-2018)
+ [2017](#release-notes-2017)

## Athena release notes for 2022<a name="release-notes-2022"></a>

### August 3, 2022<a name="release-note-2022-08-03"></a>

Published on 2022\-08\-03

Athena releases JDBC driver version 2\.0\.32\. The JDBC 2\.0\.32 driver includes the following changes:
+ The `User-Agent` string sent to the Athena SDK has been extended to contain the driver version, JDBC specification version, and the name of the authentication plugin\. 
+  Fixed a `NullPointerException` that was thrown when no value was provided for the `CheckNonProxyHost` parameter\. 
+  Fixed an issue with `login_url` parsing in the BrowserSaml authentication plugin\. 
+  Fixed a proxy host issue that occurred when the `UseProxyforIdp` parameter was set to `true`\. 

For more information, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### August 1, 2022<a name="release-note-2022-08-01"></a>

Published on 2022\-08\-01

Athena announces improvements to the Athena Query Federation SDK and Athena prebuilt data source connectors\. The improvements include the following:
+ **Struct parsing** – Fixed a `GlueFieldLexer` parsing issue in the Athena Query Federation SDK that prevented certain complicated structs from displaying all of their data\. This issue affected connectors built on the Athena Query Federation SDK\.
+ **AWS Glue tables** – Added additional support for the `set` and `decimal` column types in AWS Glue tables\.
+ **DynamoDB connector** – Added the ability to ignore casing on DynamoDB attribute names\. For more information, see the [disable\_projection\_and\_casing](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-dynamodb#disable_projection_and_casing) section of the [Amazon Athena DynamoDB connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-dynamodb) documentation on GitHub\.

For more information, see [Release v2022\.30\.2 of Athena Query Federation](https://github.com/awslabs/aws-athena-query-federation/releases/tag/v2022.30.2) on GitHub\.

### July 21, 2022<a name="release-note-2022-07-21"></a>

Published on 2022\-07\-21

You can now analyze and debug your queries using performance metrics and interactive, visual query analysis tools in the Athena console\. The query performance data and execution details can help you identify bottlenecks in queries, inspect the operators and statistics for each stage of a query, trace the volume of data flowing between stages, and validate the impact of query predicates\. You can now:
+ Access the distributed and logical execution plan for your query in a single click\. 
+ Explore the operations at each stage before the stage is run\.
+ Visualize the performance of completed queries with metrics for time spent in the queuing, planning, and execution stages\. 
+ Get information about the number of rows and amount of source data processed and output by your query\.
+ See granular execution details for your queries presented in context and formatted as an interactive graph\.
+ Use precise, stage\-level execution details to understand the flow of data through your query\.
+ Analyze query performance data programmatically using new APIs to [get query runtime statistics](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryRuntimeStatistics.html), also released today\.

To learn how to use these capabilities on your queries, watch the video tutorial [Optimize Amazon Athena Queries with New Query Analysis Tools](https://www.youtube.com/watch?v=7JUyTqglmNU) on the AWS YouTube channel\.

For documentation, see [Viewing execution plans for SQL queries](query-plans.md) and [Viewing statistics and execution details for completed queries](query-stats.md)\.

### July 11, 2022<a name="release-note-2022-07-11"></a>

Published on 2022\-07\-11

You can now run parameterized queries directly from the Athena console or API without preparing SQL statements in advance\.

When you run queries in the Athena console that have parameters in the form of question marks, the user interface now prompts you to enter values for the parameters directly\. This eliminates the need to modify literal values in the query editor every time you want to run the query\. 

If you use the enhanced [query execution](https://docs.aws.amazon.com/athena/latest/APIReference/API_StartQueryExecution.html) API, you can now provide the execution parameters and their values in a single call\.

For more information, see [Using parameterized queries](querying-with-prepared-statements.md) in this user guide and the AWS Big Data Blog post [Use Amazon Athena parameterized queries to provide data as a service](http://aws.amazon.com/blogs/big-data/use-amazon-athena-parameterized-queries-to-provide-data-as-a-service/)\.

### July 8, 2022<a name="release-note-2022-07-08"></a>

Published on 2022\-07\-08

Athena announces the following fixes and improvements\.
+ Fixed an issue with `DATE` column conversion handling for SageMaker endpoints \(UDF\) that was causing query failures\.

### June 6, 2022<a name="release-note-2022-06-06"></a>

Published on 2022\-06\-06

Athena releases JDBC driver version 2\.0\.31\. The JDBC 2\.0\.31 driver includes the following changes:
+ **log4j dependency issue** – Resolved a Cannot find driver class error message caused by a log4j dependency\.

For more information, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### May 25, 2022<a name="release-note-2022-05-25"></a>

Published on 2022\-05\-25

Athena announces the following fixes and improvements\.
+ **Iceberg support**
  + Introduced support for cross\-region queries\. Now you can query Iceberg tables in an AWS Region that is different from the AWS Region that you are using\.
  + Introduced support for server side encryption configuration\. Now you can use [SSE\-S3/SSE\-KMS](https://docs.aws.amazon.com/AmazonS3/latest/userguide/specifying-kms-encryption.html) to encrypt data from Iceberg write operations in Amazon S3\. 

  For more information about using Apache Iceberg in Athena, see [Using Iceberg tables](querying-iceberg.md)\.
+ **JDBC 2\.0\.30 driver release** 

  The JDBC 2\.0\.30 driver for Athena has the following improvements:
  + Fixes a data race issue that affected parameterized prepared statements\.
  + Fixes an application start up issue that occurred in Gradle build environments\.

  To download the JDBC 2\.0\.30 driver, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### May 6, 2022<a name="release-note-2022-05-06"></a>

Published on 2022\-05\-06

Released the JDBC 2\.0\.29 and ODBC 1\.1\.17 drivers for Athena\.

These drivers include the following changes:
+ Updated the SAML plugin browser launch process\.

For more information about these changes, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### April 22, 2022<a name="release-note-2022-04-22"></a>

Published on 2022\-04\-22

Athena announces the following fixes and improvements\.
+ Fixed an issue in the [partition indices and filtering feature](http://aws.amazon.com/blogs/big-data/improve-amazon-athena-query-performance-using-aws-glue-data-catalog-partition-indexes/) with the partition cache that occurred when the following conditions were met:
  + The `partition_filtering.enabled` key was set to `true` in the AWS Glue table properties for a table\.
  + The same table was used multiple times with different partition filter values\.

### April 21, 2022<a name="release-note-2022-04-21"></a>

Published on 2022\-04\-21

You can now use Amazon Athena to run federated queries on new data sources, including Google BigQuery, Azure Synapse, and Snowflake\. New data source connectors include:
+ [Azure Data Lake Storage \(ADLS\) Gen2](athena-prebuilt-data-connectors-adls-gen2.md)
+ [Azure Synapse](athena-prebuilt-data-connectors-azure-synapse.md)
+ [Cloudera Hive](athena-prebuilt-data-connectors-cloudera-hive.md)
+ [Cloudera Impala](athena-prebuilt-data-connectors-cloudera-impala.md)
+ [Google BigQuery](athena-prebuilt-data-connectors-google-bigquery.md)
+ [Hortonworks](athena-prebuilt-data-connectors-hortonworks.md)
+ [Microsoft SQL Server](athena-prebuilt-data-connectors-microsoft-sql-server.md)
+ [Oracle](athena-prebuilt-data-connectors-oracle.md)
+ [SAP HANA \(Express Edition\)](athena-prebuilt-data-connectors-sap-hana.md)
+ [Snowflake](athena-prebuilt-data-connectors-snowflake.md)
+ [Teradata](athena-prebuilt-data-connectors-teradata.md)

For a complete list of data sources supported by Athena, see [Using Athena data source connectors](athena-prebuilt-data-connectors.md)\.

To make it easier to browse the available sources and connect to your data, you can now search, sort, and filter the available connectors from an updated **Data Sources** screen in the Athena console\.

To learn about querying federated sources, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md) and [Writing federated queries](writing-federated-queries.md)\.

### April 13, 2022<a name="release-note-2022-04-13"></a>

Published on 2022\-04\-13

Athena releases JDBC driver version 2\.0\.28\. The JDBC 2\.0\.28 driver includes the following changes:
+ **JWT support** – The driver now supports JSON web tokens \(JWT\) for authentication\. For information about using JWT with the JDBC driver, see the installation and configuration guide, downloadable from the [JDBC driver page](connect-with-jdbc.md)\.
+ **Updated Log4j libraries** – The JDBC driver now uses the following Log4j libraries:
  + Log4j\-api 2\.17\.1 \(previously 2\.17\.0\)
  + Log4j\-core 2\.17\.1 \(previously 2\.17\.0\) 
  + Log4j\-jcl 2\.17\.2
+ **Other improvements** – The new driver also includes the following improvements and bug fixes:
  + The Athena prepared statements feature is now available through JDBC\. For information about prepared statements, see [Using parameterized queries](querying-with-prepared-statements.md)\.
  + Athena JDBC SAML federation is now functional for the China Regions\.
  + Additional minor improvements\.

For more information, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### March 30, 2022<a name="release-note-2022-03-30"></a>

Published on 2022\-03\-30

Athena announces the following fixes and improvements\.
+ **Cross\-region querying** – You can now use Athena to query data located in an Amazon S3 bucket across AWS Regions including Asia Pacific \(Hong Kong\), Middle East \(Bahrain\), Africa \(Cape Town\), and Europe \(Milan\)\.
  + For a list of AWS Regions in which Athena is available, see [Amazon Athena endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/athena.html)\.
  + For information about enabling an AWS Region that is disabled by default, see [Enabling a Region](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html#rande-manage-enable)\. 
  + For information about querying across Regions, see [Querying across regions](querying-across-regions.md)\. 

### March 18, 2022<a name="release-note-2022-03-18"></a>

Published on 2022\-03\-18

Athena announces the following fixes and improvements\.
+ **Dynamic filtering** – [Dynamic filtering](#release-note-2021-07-30) has been improved for integer columns by efficiently applying the filter to each record of a corresponding table\. 
+ **Iceberg** – Fixed an issue that caused failures when writing Iceberg Parquet files larger than 2GB\. 
+ **Uncompressed output** – [CREATE TABLE](create-table.md) statements now support writing uncompressed files\. To write uncompressed files, use the following syntax: 
  + CREATE TABLE \(text file or JSON\) – In `TBLPROPERTIES`, specify `write.compression = NONE`\.
  + CREATE TABLE \(Parquet\) – In `TBLPROPERTIES`, specify `parquet.compression = UNCOMPRESSED`\.
  + CREATE TABLE \(ORC\) – In `TBLPROPERTIES`, specify `orc.compress = NONE`\.
+ **Compression** – Fixed an issue with inserts for text file tables that created files compressed in one format but used another compression format file extension when non\-default compression methods were used\. 
+ **Avro** – Fixed issues that occurred when reading decimals of the fixed type from Avro files\. 

### March 2, 2022<a name="release-note-2022-03-02"></a>

Published on 2022\-03\-02

Athena announces the following features and improvements\.
+ You can now grant the Amazon S3 bucket owner full control access over query results when [ACLs are enabled](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) for the query result bucket\. For more information, see [Specifying a query result location](querying.md#query-results-specify-location)\.
+ You can now update existing named queries\. For more information, see [Using saved queries](saved-queries.md)\.

### February 23, 2022<a name="release-note-2022-02-23"></a>

Published on 2022\-02\-23

Athena announces the following fixes and performance improvements\.
+ Memory handling improvements to enhance performance and reduce memory errors\.
+ Athena now reads ORC timestamp columns with time zone information stored in stripe footers and writes ORC files with time zone \(UTC\) in footers\. This only impacts the behavior of ORC timestamp reads if the ORC file to be read was created in a non\-UTC time zone environment\.
+ Fixed incorrect symlink table size estimates that resulted in suboptimal query plans\.
+ Lateral exploded views can now be queried in the Athena console from Hive metastore data sources\.
+ Improved Amazon S3 read error messages to include more detailed [Amazon S3 error code](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html#ErrorCodeList) information\.
+ Fixed an issue that caused output files in ORC format to become incompatible with Apache Hive 3\.1\.
+ Fixed an issue that caused table names with quotes to fail in certain DML and DDL queries\.

### February 15, 2022<a name="release-note-2022-02-15"></a>

Published on 2022\-02\-15

Amazon Athena has increased the active DML query quota in all AWS Regions\. Active queries include both running and queued queries\. With this change, you can now have more DML queries in an active state than before\.

For information about Athena service quotas, see [Service Quotas](service-limits.md)\. For the query quotas in the Region where you use Athena, see [Amazon Athena endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/athena.html#amazon-athena-limits) in the *AWS General Reference*\.

To monitor your quota usage, you can use CloudWatch usage metrics\. Athena publishes the `ActiveQueryCount` metric in the `AWS/Usage` namespace\. For more information, see [Monitoring Athena usage metrics](monitoring-athena-usage-metrics.md)\.

After reviewing your usage, you can use the [Service Quotas](https://console.aws.amazon.com/servicequotas/) console to request a quota increase\. If you previously requested a quota increase for your account, your requested quota still applies if it exceeds the new default active DML query quota\. Otherwise, all accounts use the new default\.

### February 14, 2022<a name="release-note-2022-02-14"></a>

Published on 2022\-02\-14

This release adds the `ErrorType` subfield to the [AthenaError](https://docs.aws.amazon.com/athena/latest/APIReference/API_AthenaError.html) response object in the Athena [GetQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryExecution.html) API action\.

While the existing `ErrorCategory` field indicates the general source of a failed query \(system, user, or other\), the new `ErrorType` field provides more granular information about the error that occurred\. Combine the information from both fields to gain insight into the causes of query failure\. 

For more information, see [Athena error catalog](error-reference.md)\.

### February 9, 2022<a name="release-note-2022-02-09"></a>

Published on 2022\-02\-09

The old Athena console is no longer available\. Athena's new console supports all of the features of the earlier console, but with an easier to use, modern interface and includes new features that improve the experience of developing queries, analyzing data, and managing your usage\. To use the new Athena console, visit [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

### February 8, 2022<a name="release-note-2022-02-08"></a>

Published on 2022\-02\-08

**Expected bucket owner** – As an added security measure, you can now optionally specify the AWS account ID that you expect to be the owner of your query results output location bucket in Athena\. If the account ID of the query results bucket owner does not match the account ID that you specify, attempts to output to the bucket will fail with an Amazon S3 permissions error\. You can make this setting at the client or workgroup level\.

For more information, see [Specifying a query result location](querying.md#query-results-specify-location)\.

### January 28, 2022<a name="release-note-2022-01-28"></a>

Published on 2022\-01\-28

Athena announces the following engine feature enhancements\.
+ **Apache Hudi** – Snapshot queries on Hudi Merge on Read \(MoR\) tables can now read timestamp columns that have the `INT64` data type\.
+ **UNION queries** – Performance improvement and data scan reduction for certain `UNION` queries that scan the same table multiple times\.
+ **Disjunct queries** – Performance improvement for queries that have only disjunct values for each partition column on the filter\.
+ **Partition projection enhancements**
  + Multiple disjunct values are now allowed on the filter condition for columns of the `injected` type\. For more information, see [Injected type](partition-projection-supported-types.md#partition-projection-injected-type)\.
  + Performance improvement for columns of string\-based types like `CHAR` or `VARCHAR` that have only disjunct values on the filter\.

### January 13, 2022<a name="release-note-2022-01-13"></a>

Published on 2022\-01\-13

Released the JDBC 2\.0\.27 and ODBC 1\.1\.15 drivers for Athena\.

The JDBC 2\.0\.27 driver includes the following changes:
+ The driver has been updated to retrieve external catalogs\. 
+ The extended driver version number is now included in the `user-agent` string as part of the Athena API call\.

The ODBC 1\.1\.15 driver includes the following changes:
+ Corrects an issue with second calls to `SQLParamData()`\.

For more information about these changes, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

## Athena release notes for 2021<a name="release-notes-2021"></a>

### November 30, 2021<a name="release-note-2021-11-30"></a>

Published on 2021\-11\-30

Amazon Athena users can now use AWS Lake Formation to configure fine\-grained access permissions and read from ACID\-compliant tables\. Amazon Athena makes it simple to analyze data in Amazon S3 based data lakes to help ensure that users only have access to data to which they're authorized\. The ACID features help ensure that their queries are reliable in the face of complex changes to the underlying data\.

Use [Lake Formation data filtering ](https://docs.aws.amazon.com/lake-formation/latest/dg/data-filtering-overview.html) to secure the tables in your Amazon S3 data lake by granting permissions at the column, row, and cell levels\. These permissions are enforced when Athena users query your data\. This fine level of control means that you can grant users access to sensitive information without using course\-grained masking that would otherwise impede their analyses\. Furthermore, with Lake Formation governed tables, Athena users can query data while multiple users simultaneously add and delete the table's Amazon S3 data objects\.

For more information, see [Using Athena ACID transactions](acid-transactions.md) and [Using governed tables](lf-governed-tables.md)\.

### November 26, 2021<a name="release-note-2021-11-26"></a>

Published on 2021\-11\-26

Athena announces the public preview of Athena ACID transactions, which add write, delete, update, and time travel operations to Athena's SQL data manipulation language \(DML\)\. Athena ACID transactions enable multiple concurrent users to make reliable, row\-level modifications to Amazon S3 data\. Built on the [Apache Iceberg](https://iceberg.apache.org) table format, Athena ACID transactions are compatible with other services and engines such as [Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html) and [Apache Spark](https://spark.apache.org/) that also support the Iceberg table formats\.

Athena ACID transactions and familiar SQL syntax simplify updates to your business and regulatory data\. For example, to respond to a data erasure request, you can perform a SQL `DELETE` operation\. To make manual record corrections, you can use a single `UPDATE` statement\. To recover data that was recently deleted, you can issue time travel queries using a `SELECT` statement\. Athena transactions are available through Athena's console, API operations, and ODBC and JDBC drivers\.

For more information, see [Using Athena ACID transactions](acid-transactions.md)\.

### November 24, 2021<a name="release-note-2021-11-24"></a>

Published on 2021\-11\-24

Athena announces support for reading and writing [ZStandard](http://facebook.github.io/zstd/) compressed ORC, Parquet, and textfile data\. Athena uses ZStandard compression level 3 when writing ZStandard compressed data\. 

For information about data compression in Athena, see [Athena compression support](compression-formats.md)\.

### November 22, 2021<a name="release-note-2021-11-22"></a>

Published on 2021\-11\-22

You can now manage AWS Step Functions workflows from the Amazon Athena console, making it easier to build scalable data processing pipelines, execute queries based on custom business logic, automate administrative and alerting tasks, and more\.

Step Functions is now integrated with Athena's upgraded console, and you can use it to view an interactive workflow diagram of your state machines that invoke Athena\. To get started, select **Workflows** from the left navigation panel\. If you have existing state machines with Athena queries, select a state machine to view an interactive diagram of the workflow\. If you are new to Step Functions, you can get started by launching a sample project from the Athena console and customizing it to suit your use cases\.

For more information, see [Build and orchestrate ETL pipelines using Amazon Athena and AWS Step Functions](http://aws.amazon.com/blogs/big-data/build-and-orchestrate-etl-pipelines-using-amazon-athena-and-aws-step-functions/), or consult the [Step Functions documentation](https://docs.aws.amazon.com/step-functions/latest/dg/connect-athena.html)\.

### November 18, 2021<a name="release-note-2021-11-18"></a>

Published on 2021\-11\-18

Athena announces new features and improvements\.
+ Support for spill\-to\-disk for aggregation queries that contain `DISTINCT`, `ORDER BY`, or both, as in the following example:

  ```
  SELECT array_agg(orderstatus ORDER BY orderstatus)
  FROM orders
  GROUP BY orderpriority, custkey
  ```
+ Addressed memory handling issues for queries that use `DISTINCT`\. To avoid error messages like Query exhausted resources at this scale factor when you use `DISTINCT` queries, choose columns that have a low cardinality for `DISTINCT`, or reduce the data size of the query\.
+ In `SELECT COUNT(*)` queries that do not specify a specific column, improved performance and memory usage by keeping only the count without row buffering\.
+ Introduced the following string functions\.
  + `translate(source, from, to)` – Returns the `source` string with the characters found in the `from` string replaced by the corresponding characters in the `to` string\. If the `from` string contains duplicates, only the first is used\. If the `source` character does not exist in the `from` string, the `source` character is copied without translation\. If the index of the matching character in the `from` string is greater than the length of the `to` string, the character is omitted from the resulting string\.
  + `concat_ws(string0, array(varchar))` – Returns the concatenation of elements in the array using `string0` as a separator\. If `string0` is null, then the return value is null\. Any null values in the array are skipped\.
+ Fixed a bug in which queries failed when trying to access a missing subfield in a `struct`\. Queries now return a null for the missing subfield\.
+ Fixed an issue with inconsistent hashing for the decimal data type\.
+ Fixed an issue that caused exhausted resources when there were too many columns in a partition\.

### November 17, 2021<a name="release-note-2021-11-17"></a>

Published on 2021\-11\-17

[Amazon Athena](http://aws.amazon.com/athena/) now supports partition indexing to accelerate queries on partitioned tables in the [AWS Glue Data Catalog](http://aws.amazon.com/glue/)\.

When querying partitioned tables, Athena retrieves and filters the available table partitions to the subset relevant to your query\. As new data and partitions are added, more time is required to process the partitions and query runtime can increase\. To optimize partition processing and improve query performance on highly partitioned tables, Athena now supports [AWS Glue partition indexes](https://docs.aws.amazon.com/glue/latest/dg/partition-indexes.html)\.

For more information, see [AWS Glue partition indexing and filtering](glue-best-practices.md#glue-best-practices-partition-index)\.

### November 16, 2021<a name="release-note-2021-11-16"></a>

Published on 2021\-11\-16

The new and improved [Amazon Athena](http://aws.amazon.com/athena/) console is now generally available in the AWS commercial and GovCloud regions where [Athena is available](http://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)\. Athena's new console supports all of the features of the earlier console, but with an easier to use, modern interface and includes new features that improve the experience of developing queries, analyzing data, and managing your usage\. You can now:
+ Rearrange, navigate to, or close multiple query tabs from a redesigned query tab bar\.
+ Read and edit queries more easily with improved SQL and text formatting\.
+ Copy query results to your clipboard in addition to downloading the full result set\.
+ Sort your query history, saved queries, and workgroups, and choose which columns to show or hide\.
+ Use a simplified interface to configure data sources and workgroups in fewer clicks\.
+ Set preferences for displaying query results, query history, line wrapping, and more\.
+ Increase your productivity with new and improved keyboard shortcuts and embedded product documentation\.

With today's announcement, the [redesigned console](https://console.aws.amazon.com/athena/home) is now the default\. To tell us about your experience, choose **Feedback** in the bottom\-left corner of the console\.

If desired, you may use the earlier console by logging into your AWS account, choosing Amazon Athena, and deselecting **New Athena experience** from the navigation panel on the left\.

### November 12, 2021<a name="release-note-2021-11-12"></a>

Published on 2021\-11\-12

You can now use Amazon Athena to run federated queries on data sources located in an AWS account other than your own\. Until today, querying this data required the data source and its connector to use the same AWS account as the user that queried the data\.

As a data administrator, you can enable cross\-account federated queries by sharing your data connector with a data analyst's account\. As a data analyst, you can add a data connector that a data administrator has shared with you to your account\. Configuration changes to the connector in the originating account apply automatically to the shared connector\.

For information about enabling cross\-account federated queries, see [Enabling cross\-account federated queries](xacct-fed-query-enable.md)\. To learn about querying federated sources, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md) and [Writing federated queries](writing-federated-queries.md)\.

### November 2, 2021<a name="release-note-2021-11-02"></a>

Published on 2021\-11\-02

You can now use the `EXPLAIN ANALYZE` statement in Athena to view the distributed execution plan and cost of each operation for your SQL queries\.

For more information, see [Using EXPLAIN and EXPLAIN ANALYZE in Athena](athena-explain-statement.md)\.

### October 29, 2021<a name="release-note-2021-10-29"></a>

Published on 2021\-10\-29

Athena releases JDBC 2\.0\.25 and ODBC 1\.1\.13 drivers and announces features and improvements\.

#### JDBC and ODBC Drivers<a name="release-note-2021-10-29-jdbc-odbc"></a>

Released JDBC 2\.0\.25 and ODBC 1\.1\.13 drivers for Athena\. Both drivers offer support for browser SAML multi\-factor authentication, which can be configured to work with any SAML 2\.0 provider\.

The JDBC 2\.0\.25 driver includes the following changes:
+ Support for browser SAML authentication\. The driver includes a browser SAML plugin which can be configured to work with any SAML 2\.0 provider\.
+ Support for AWS Glue API calls\. You can use the `GlueEndpointOverride` parameter to override the AWS Glue endpoint\. 
+ Changed the `com.simba.athena.amazonaws` class path to `com.amazonaws`\.

The ODBC 1\.1\.13 driver includes the following changes:
+ Support for browser SAML authentication\. The driver includes a browser SAML plugin which can be configured to work with any SAML 2\.0 provider\. For an example of how to use the browser SAML plugin with the ODBC driver, see [Configuring single sign\-on using ODBC, SAML 2\.0, and the Okta Identity Provider](okta-saml-sso.md)\.
+ You can now configure the role session duration when you use ADFS, Azure AD, or Browser Azure AD for authentication\.

For more information about these and other changes, and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

#### Features and Improvements<a name="release-note-2021-10-29-features-improvements"></a>

Athena announces the following features and improvements\.
+ A new optimization rule has been introduced to avoid duplicate table scans in certain cases\.

### October 4, 2021<a name="release-note-2021-10-04"></a>

Published on 2021\-10\-04

Athena announces the following features and improvements\.
+ **SQL OFFSET** – The SQL `OFFSET` clause is now supported in `SELECT` statements\. For more information, see [SELECT](select.md)\.
+  **CloudWatch usage metrics** – Athena now publishes the `ActiveQueryCount` metric in the `AWS/Usage` namespace\. For more information, see [Monitoring Athena usage metrics](monitoring-athena-usage-metrics.md)\.
+ **Query planning** – Fixed a bug that could in rare cases cause query planning timeouts\.

### September 16, 2021<a name="release-note-2021-09-16"></a>

Published on 2021\-09\-16

Athena announces the following new features and improvements\.

#### Features<a name="release-note-2021-09-16-features"></a>
+ The Apache Hudi Metadata Listing Feature is now available for Hudi tables, reducing Amazon S3 overhead and query times for partitioned table queries\. For information about using Apache Hudi in Athena, see [Using Athena to query Apache Hudi datasets](querying-hudi.md)\.
+ Added support for specifying textfile and JSON compression in CTAS using the `write_compression` table property\. You can also specify the `write_compression` property in CTAS for the Parquet and ORC formats\. For more information, see [CTAS table properties](create-table-as.md#ctas-table-properties)\.
+ The BZIP2 compression format is now supported for writing textfile and JSON files\. For more information about the compression formats in Athena, see [Athena compression support](compression-formats.md)\.

#### Improvements<a name="release-note-2021-09-16-improvements"></a>
+ Fixed a bug in which identity information failed to be sent to the UDF Lambda function\.
+ Fixed a predicate pushdown issue with disjunct filter conditions\.
+ Fixed a hashing issue for decimal types\.
+ Fixed an unnecessary statistics collection issue\.
+ Removed an inconsistent error message\.
+ Improved broadcast join performance by applying dynamic partition pruning in the worker node\.
+ For federated queries:
  + Altered configuration to reduce the occurrence of `CONSTRAINT_VIOLATION` errors in federated queries\.

### September 15, 2021<a name="release-note-2021-09-15"></a>

Published on 2021\-09\-15

You can now use a redesigned Amazon Athena console \(Preview\)\. A new Athena JDBC driver has been released\.

#### Athena Console Preview<a name="release-note-2021-09-15-console-preview"></a>

You can now use a redesigned [Amazon Athena](http://aws.amazon.com/athena/) console \(Preview\) from any AWS Region where Athena is available\. The new console supports all of the features of the existing console, but from an easier to use, modern interface\.

To switch to the new [console](https://console.aws.amazon.com/athena/home), log into your AWS account and choose Amazon Athena\. From the AWS console navigation bar, choose **Switch to the new console**\. To return to the default console, deselect **New Athena experience** from the navigation panel on the left\.

Get started with the new [console](https://console.aws.amazon.com/athena/home) today\. Choose **Feedback** in the bottom\-left corner to tell us about your experience\.

#### Athena JDBC Driver 2\.0\.24<a name="release-note-2021-09-15-jdbc-2-0-24"></a>

Athena announces availability of JDBC driver version 2\.0\.24 for Athena\. This release updates proxy support for all credentials providers\. The driver now supports proxy authentication for all hosts that are not supported by the `NonProxyHosts` connection property\. 

As a convenience, this release includes downloads of the JDBC driver both with and without the AWS SDK\. This JDBC driver version allows you to have both the AWS\-SDK and the Athena JDBC driver embedded in project\.

For more information and to download the new driver, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### August 31, 2021<a name="release-note-2021-08-31"></a>

Published on 2021\-08\-31

Athena announces the following feature enhancements and bug fixes\.
+ **Athena federation enhancements** – Athena has added support to map types and better support for complex types as part of the [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases)\. This version also includes some memory enhancements and performance optimizations\.
+ **New error categories** – Introduced the `USER` and `SYSTEM` error categories in error messages\. These categories help you distinguish between errors that you can fix yourself \(`USER`\) and errors that can require assistance from Athena support \(`SYSTEM`\)\.
+ **Federated query error messaging** – Updated `USER_ERROR` categorizations for federated query related errors\.
+ **JOIN** – Fixed spill\-to\-disk related bugs and memory issues to enhance performance and reduce memory errors in `JOIN` operations\.

### August 12, 2021<a name="release-note-2021-08-12"></a>

Published on 2021\-08\-12

Released the ODBC 1\.1\.12 driver for Athena\. This version corrects issues related to `SQLPrepare()`, `SQLGetInfo()`, and `EndpointOverride`\.

To download the new driver, release notes, and documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### August 6, 2021<a name="release-note-2021-08-06"></a>

Published on 2021\-08\-06

Amazon Athena announces availability of Athena and its [features](http://aws.amazon.com/athena/features/) in the Asia Pacific \(Osaka\) Region\.

This release expands Athena's availability in Asia Pacific to include Asia Pacific \(Hong Kong\), Asia Pacific \(Mumbai\), Asia Pacific \(Osaka\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), and Asia Pacific \(Tokyo\)\. For a complete list of AWS services available in these and other Regions, refer to the [AWS Regional Services List](http://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)\.

### August 5, 2021<a name="release-note-2021-08-05"></a>

Published on 2021\-08\-05

You can use the `UNLOAD` statement to write the output of a `SELECT` query to the PARQUET, ORC, AVRO, and JSON formats\.

For more information, see [UNLOAD](unload.md)\.

### July 30, 2021<a name="release-note-2021-07-30"></a>

Published on 2021\-07\-30

Athena announces the following feature enhancements and bug fixes\.
+ **Dynamic filtering and partition pruning** – Improvements increase performance and reduce the amount of data scanned in certain queries, as in the following example\. 

  This example assumes that `Table_B` is an unpartitioned table that has file sizes that add up to less than 20Mb\. For queries like this, less data is read from `Table_A` and the query completes more quickly\.

  ```
  SELECT *
  FROM Table_A
  JOIN Table_B ON Table_A.date = Table_B.date
  WHERE Table_B.column_A = "value"
  ```
+ **ORDER BY with LIMIT, DISTINCT with LIMIT** – Performance improvements to queries that use `ORDER BY` or `DISTINCT` followed by a `LIMIT` clause\.
+ **S3 Glacier Deep Archive files** – When Athena queries a table that contains a mix of [S3 Glacier Deep Archive files](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html#sc-glacier) and non\-S3 Glacier files, Athena now skips the S3 Glacier Deep Archive files for you\. Previously, you were required to manually move these files from the query location or the query would fail\. If you want to use Athena to query objects in S3 Glacier Deep Archive storage, you must restore them\. For more information, see [Restoring an archived object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects.html) in the *Amazon S3 User Guide*\.
+ Fixed a bug in which empty files created by the CTAS `bucketed_by` [table property](create-table-as.md#ctas-table-properties) were not encrypted correctly\.

### July 21, 2021<a name="release-note-2021-07-21"></a>

Published on 2021\-07\-21

With the July 2021 release of [Microsoft Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/), you can create reports and dashboards using a native data source connector for Amazon Athena\. The connector for Amazon Athena is available as a standard connector in Power BI, supports [DirectQuery](https://docs.microsoft.com/power-bi/connect-data/desktop-use-directquery), and enables analysis on large datasets and content refresh through [Power BI Gateway](https://powerbi.microsoft.com/gateway/)\.

Because the connector uses your existing ODBC data source name \(DSN\) to connect to and run queries on Athena, it requires the Athena ODBC driver\. To download the latest ODBC driver, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

For more information, see [Using the Amazon Athena Power BI connector](connect-with-odbc-and-power-bi.md)\.

### July 16, 2021<a name="release-note-2021-07-16"></a>

Published on 2021\-07\-16

Amazon Athena has updated its integration with Apache Hudi\. Hudi is an open\-source data management framework used to simplify incremental data processing in Amazon S3 data lakes\. The updated integration enables you to use Athena to query Hudi 0\.8\.0 tables managed through Amazon EMR, Apache Spark, Apache Hive or other compatible services\. In addition, Athena now supports two additional features: snapshot queries on Merge\-on\-Read \(MoR\) tables and read support on bootstrapped tables\.

Apache Hudi provides record\-level data processing that can help you simplify development of Change Data Capture \(CDC\) pipelines, comply with GDPR\-driven updates and deletes, and better manage streaming data from sensors or devices that require data insertion and event updates\. The 0\.8\.0 release makes it easier to migrate large Parquet tables to Hudi without copying data so you can query and analyze them through Athena\. You can use Athena's new support for snapshot queries to have near real\-time views of your streaming table updates\.

To learn more about using Hudi with Athena, see [Using Athena to query Apache Hudi datasets](querying-hudi.md)\.

### July 8, 2021<a name="release-note-2021-07-08"></a>

Published on 2021\-07\-08

Released the ODBC 1\.1\.11 driver for Athena\. The ODBC driver can now authenticate the connection using a JSON Web Token \(JWT\)\. On Linux, the default value for the Workgroup property has been set to Primary\.

For more information and to download the new driver, release notes, and documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### July 1, 2021<a name="release-note-2021-07-01"></a>

Published on 2021\-07\-01

On July 1, 2021, special handling of preview workgroups ended\. While `AmazonAthenaPreviewFunctionality` workgroups retain their name, they no longer have special status\. You can continue to use `AmazonAthenaPreviewFunctionality` workgroups to view, modify, organize, and run queries\. However, queries that use features that were formerly in preview are now subject to standard Athena billing terms and conditions\. For billing information, see [Amazon Athena pricing](http://aws.amazon.com/athena/pricing/)\.

### June 23, 2021<a name="release-note-2021-06-23"></a>

Published on 2021\-06\-23

Released JDBC 2\.0\.23 and ODBC 1\.1\.10 drivers for Athena\. Both drivers offer improved read performance and support [EXPLAIN](athena-explain-statement.md) statements and [parameterized queries](querying-with-prepared-statements.md)\. 

`EXPLAIN` statements show the logical or distributed execution plan of a SQL query\. Parameterized queries enable the same query to be used multiple times with different values supplied at run time\.

The JDBC release also adds support for Active Directory Federation Services 2019 and a custom endpoint override option for AWS STS\. The ODBC release fixes an issue with IAM profile credentials\. 

For more information and to download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### May 12, 2021<a name="release-note-2021-05-12"></a>

Published on 2021\-05\-12

You can now use Amazon Athena to register an AWS Glue catalog from an account other than your own\. After you configure the required IAM permissions for AWS Glue, you can use Athena to run cross\-account queries\.

For more information, see [Registering an AWS Glue Data Catalog from another account](data-sources-glue-cross-account.md) and [Cross\-account access to AWS Glue data catalogs](security-iam-cross-account-glue-catalog-access.md)\.

### May 10, 2021<a name="release-note-2021-05-10"></a>

Published on 2021\-05\-10

Released ODBC driver version 1\.1\.9\.1001 for Athena\. This version fixes an issue with the `BrowserAzureAD` authentication type when using Azure Active Directory \(AD\)\.

To download the new drivers, release notes, and documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### May 5, 2021<a name="release-note-2021-05-05"></a>

Published on 2021\-05\-05

You can now use the Amazon Athena Vertica connector in federated queries to query Vertica data sources from Athena\. For example, you can run analytical queries over a data warehouse on Vertica and a data lake in Amazon S3\.

To deploy the Athena Vertica connector, visit the [AthenaVerticaConnector](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaVerticaConnector) page in the AWS Serverless Application Repository\.

The Amazon Athena Vertica connector exposes several configuration options through Lambda environment variables\. For information about configuration options, parameters, connection strings, deployment, and limitations, see [Amazon Athena Vertica Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-vertica) on GitHub\. 

For in\-depth information about using the Vertica connector, see [Querying a Vertica data source in Amazon Athena using the Athena Federated Query SDK](http://aws.amazon.com/blogs/big-data/querying-a-vertica-data-source-in-amazon-athena-using-the-athena-federated-query-sdk/) in the *AWS Big Data Blog*\.

### April 30, 2021<a name="release-note-2021-04-30"></a>

Published on 2021\-04\-30

Released drivers JDBC 2\.0\.21 and ODBC 1\.1\.9 for Athena\. Both releases support SAML authentication with Azure Active Directory \(AD\) and SAML authentication with PingFederate\. The JDBC release also supports parameterized queries\. For information about parameterized queries in Athena, see [Using parameterized queries](querying-with-prepared-statements.md)\. 

To download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### April 29, 2021<a name="release-note-2021-04-29"></a>

Published on 2021\-04\-29

Amazon Athena announces availability of Athena engine version 2 in the China \(Beijing\) and China \(Ningxia\) Regions\.

For information about Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.

### April 26, 2021<a name="release-note-2021-04-26"></a>

Published on 2021\-04\-26

Window value functions in Athena engine version 2 now support `IGNORE NULLS` and `RESPECT NULLS`\.

For more information, see [Value Functions](https://prestodb.io/docs/0.231/functions/window.html#value-functions) in the Presto documentation\.

### April 21, 2021<a name="release-note-2021-04-21"></a>

Published on 2021\-04\-21

Amazon Athena announces availability of Athena engine version 2 in the Europe \(Milan\) and Africa \(Cape Town\) Regions\.

For information about Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.

### April 5, 2021<a name="release-note-2021-04-05"></a>

Published on 2021\-04\-05

#### EXPLAIN Statement<a name="release-note-2021-04-05-explain-statement"></a>

You can now use the `EXPLAIN` statement in Athena to view the execution plan for your SQL queries\.

For more information, see [Using EXPLAIN and EXPLAIN ANALYZE in Athena](athena-explain-statement.md) and [Understanding Athena EXPLAIN statement results](athena-explain-statement-understanding.md)\.

#### SageMaker Machine Learning Models in SQL Queries<a name="release-note-2021-04-05-machine-learning"></a>

Machine learning model inference with Amazon SageMaker is now generally available for Amazon Athena\. Use machine learning models in SQL queries to simplify complex tasks such as anomaly detection, customer cohort analysis, and time\-series predictions by invoking a function in a SQL query\.

For more information, see [Using Machine Learning \(ML\) with Amazon Athena](querying-mlmodel.md)\.

#### User Defined Functions \(UDF\)<a name="release-note-2021-04-05-udfs"></a>

User defined functions \(UDFs\) are now generally available for Athena\. Use UDFs to leverage custom functions that process records or groups of records in a single SQL query\.

For more information, see [Querying with user defined functions](querying-udf.md)\.

### March 30, 2021<a name="release-note-2021-03-30"></a>

Published on 2021\-03\-30

Amazon Athena announces availability of Athena engine version 2 in the Asia Pacific \(Hong Kong\) and Middle East \(Bahrain\) Regions\.

For information about Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.

### March 25, 2021<a name="release-note-2021-03-25"></a>

Published on 2021\-03\-25

Amazon Athena announces availability of Athena engine version 2 in the Europe \(Stockholm\) Region\.

For information about Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.

### March 5, 2021<a name="release-note-2021-03-05"></a>

Published on 2021\-03\-05

Amazon Athena announces availability of Athena engine version 2 in the Canada \(Central\), Europe \(Frankfurt\), and South America \(São Paulo\) Regions\.

For information about Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.

### February 25, 2021<a name="release-note-2021-02-25"></a>

Published on 2021\-02\-25

Amazon Athena announces general availability of Athena engine version 2 in the Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Europe \(London\), and Europe \(Paris\) Regions\.

For information about Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.

## Athena release notes for 2020<a name="release-notes-2020"></a>

### December 16, 2020<a name="release-note-2020-12-16"></a>

Published on 2020\-12\-16

Amazon Athena announces availability of Athena engine version 2, Athena Federated Query, and AWS PrivateLink in additional Regions\.

#### Athena engine version 2 and Athena Federated Query<a name="release-note-2020-12-16-engine-v2-and-fed-query"></a>

Amazon Athena announces general availability of Athena engine version 2 and Athena Federated Query in the Asia Pacific \(Mumbai\), Asia Pacific \(Tokyo\), Europe \(Ireland\), and US West \(N\. California\) Regions\. Athena engine version 2 and federated queries are already available in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions\.

For more information, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002) and [Using Amazon Athena Federated Query](connect-to-a-data-source.md)\.

#### AWS PrivateLink<a name="release-note-2020-12-16-privatelink"></a>

AWS PrivateLink for Athena is now supported in the Europe \(Stockholm\) Region\. For information about AWS PrivateLink for Athena, see [Connect to Amazon Athena using an interface VPC endpoint](interface-vpc-endpoint.md)\.

### November 24, 2020<a name="release-note-2020-11-24"></a>

Published on 2020\-11\-24

Released drivers JDBC 2\.0\.16 and ODBC 1\.1\.6 for Athena\. These releases support, at the account level, Okta Verify multifactor authentication \(MFA\)\. You can also use Okta MFA to configure SMS authentication and Google Authenticator authentication as factors\.

To download the new drivers, release notes, and documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### November 11, 2020<a name="release-note-2020-11-11"></a>

Published on 2020\-11\-11

Amazon Athena announces general availability in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions for Athena engine version 2 and federated queries\.

#### Athena engine version 2<a name="release-note-2020-11-11-engine-version-2"></a>

Amazon Athena announces general availability of a new query engine version, Athena engine version 2, in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions\.

Athena engine version 2 includes performance enhancements and new feature capabilities such as schema evolution support for Parquet format data, additional geospatial functions, support for reading nested schema to reduce cost, and performance enhancements in JOIN and AGGREGATE operations\.
+ For information about improvements, breaking changes, and bug fixes, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.
+ For information about how to upgrade, see [Changing Athena engine versions](engine-versions-changing.md)\.
+ For information about testing queries, see [Testing queries in advance of an engine version upgrade](engine-versions-changing.md#engine-versions-testing)\.

#### Federated SQL Queries<a name="release-note-2020-11-11-federated"></a>

You can now use Athena's federated query in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions without using the `AmazonAthenaPreviewFunctionality` workgroup\. 

Use Federated SQL queries to run SQL queries across relational, non\-relational, object, and custom data sources\. With federated querying, you can submit a single SQL query that scans data from multiple sources running on premises or hosted in the cloud\.

Running analytics on data spread across applications can be complex and time consuming for the following reasons:
+ Data required for analytics is often spread across relational, key\-value, document, in\-memory, search, graph, object, time\-series and ledger data stores\.
+ To analyze data across these sources, analysts build complex pipelines to extract, transform, and load into a data warehouse so that the data can be queried\.
+ Accessing data from various sources requires learning new programming languages and data access constructs\.

Federated SQL queries in Athena eliminate this complexity by allowing users to query the data in\-place from wherever it resides\. Analysts can use familiar SQL constructs to `JOIN` data across multiple data sources for quick analysis, and store results in Amazon S3 for subsequent use\.

##### Data Source Connectors<a name="release-note-2020-11-11-federated-connectors"></a>

To process federated queries, Athena uses Athena Data Source Connectors that run on [AWS Lambda](https://aws.amazon.com/lambda/)\. The following open sourced, pre\-built connectors were written and tested by Athena\. Use them to run SQL queries in Athena on their corresponding data sources\.
+ [CloudWatch](athena-prebuilt-data-connectors-cwlogs.md)
+ [CloudWatch Metrics](athena-prebuilt-data-connectors-cwmetrics.md)
+ [DocumentDB](athena-prebuilt-data-connectors-docdb.md)
+ [DynamoDB](athena-prebuilt-data-connectors-dynamodb.md)
+ [Elasticsearch](athena-prebuilt-data-connectors-elasticsearch.md)
+ [HBase](athena-prebuilt-data-connectors-hbase.md)
+ [Neptune](athena-prebuilt-data-connectors-neptune.md)
+ [Redis](athena-prebuilt-data-connectors-redis.md)
+ [Timestream](athena-prebuilt-data-connectors-timestream.md)
+ [TPC Benchmark DS \(TPC\-DS\)](athena-prebuilt-data-connectors-tpcds.md)

##### Custom Data Source Connectors<a name="release-note-2020-11-11-federated-connectors-custom"></a>

Using [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases), developers can build connectors to any data source to enable Athena to run SQL queries against that data source\. Athena Query Federation Connector extends the benefits of federated querying beyond AWS provided connectors\. Because connectors run on AWS Lambda, you do not have to manage infrastructure or plan for scaling to peak demands\.

##### Next Steps<a name="release-note-2020-11-11-federated-next-steps"></a>
+ To learn more about the federated query feature, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md)\.
+ To get started with using an existing connector, see [Deploying a Connector and Connecting to a Data Source](http://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source-lambda.html)\.
+ To learn how to build your own data source connector using the Athena Query Federation SDK, see [Example Athena Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.

### October 22, 2020<a name="release-note-2020-10-22"></a>

Published on 2020\-10\-22

You can now call Athena with AWS Step Functions\. AWS Step Functions can control certain AWS services directly using the [Amazon States Language](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html)\. You can use Step Functions with Athena to start and stop query execution, get query results, run ad\-hoc or scheduled data queries, and retrieve results from data lakes in Amazon S3\.

For more information, see [Call Athena with Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/connect-athena.html) in the *AWS Step Functions Developer Guide*\. 

### July 29, 2020<a name="release-note-2020-07-29"></a>

Published on 2020\-07\-29

Released JDBC driver version 2\.0\.13\. This release supports using multiple [data catalogs registered with Athena](connect-to-a-data-source-lambda.md), Okta service for authentication, and connections to VPC endpoints\.

To download and use the new version of the driver, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### July 9, 2020<a name="release-note-2020-07-09"></a>

Published on 2020\-07\-09

Amazon Athena adds support for querying compacted Hudi datasets and adds the AWS CloudFormation `AWS::Athena::DataCatalog` resource for creating, updating, or deleting data catalogs that you register in Athena\.

#### Querying Apache Hudi Datasets<a name="release-note-2020-07-09-apache-hudi"></a>

Apache Hudi is an open\-source data management framework that simplifies incremental data processing\. Amazon Athena now supports querying the read\-optimized view of an Apache Hudi dataset in your Amazon S3\-based data lake\.

For more information, see [Using Athena to query Apache Hudi datasets](querying-hudi.md)\.

#### AWS CloudFormation Data Catalog Resource<a name="release-note-2020-07-09-data-catalog-cft"></a>

To use Amazon Athena's [federated query feature](https://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source.html) to query any data source, you must first register your data catalog in Athena\. You can now use the AWS CloudFormation `AWS::Athena::DataCatalog` resource to create, update, or delete data catalogs that you register in Athena\.

For more information, see [AWS::Athena::DataCatalog](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-datacatalog.html) in the *AWS CloudFormation User Guide*\.

### June 1, 2020<a name="release-note-2020-06-01"></a>

Published on 2020\-06\-01

#### Using Apache Hive Metastore as a Metacatalog with Amazon Athena<a name="release-note-2020-06-01-hive-metastore"></a>

You can now connect Athena to one or more Apache Hive metastores in addition to the AWS Glue Data Catalog with Athena\.

To connect to a self\-hosted Hive metastore, you need an Athena Hive metastore connector\. Athena provides a [reference implementation](datastores-hive-reference-implementation.md) connector that you can use\. The connector runs as an AWS Lambda function in your account\. 

For more information, see [Using Athena Data Connector for External Hive Metastore](connect-to-data-source-hive.md)\.

### May 21, 2020<a name="release-note-2020-05-21"></a>

Published on 2020\-05\-21

Amazon Athena adds support for partition projection\. Use partition projection to speed up query processing of highly partitioned tables and automate partition management\. For more information, see [Partition projection with Amazon Athena](partition-projection.md)\.

### April 1, 2020<a name="release-note-2020-04-01"></a>

Published on 2020\-04\-01

In addition to the US East \(N\. Virginia\) Region, the Amazon Athena [federated query](connect-to-a-data-source.md), [user defined functions \(UDFs\)](querying-udf.md), [machine learning inference](querying-mlmodel.md), and [external Hive metastore](connect-to-data-source-hive.md) features are now available in preview in the Asia Pacific \(Mumbai\), Europe \(Ireland\), and US West \(Oregon\) Regions\. 

### March 11, 2020<a name="release-note-2020-03-11"></a>

Published on 2020\-03\-11

Amazon Athena now publishes Amazon CloudWatch Events for query state transitions\. When a query transitions between states \-\- for example, from Running to a terminal state such as Succeeded or Cancelled \-\- Athena publishes a query state change event to CloudWatch Events\. The event contains information about the query state transition\. For more information, see [Monitoring Athena queries with CloudWatch events](athena-cloudwatch-events.md)\.

### March 6, 2020<a name="release-note-2020-03-06"></a>

Published on 2020\-03\-06

You can now create and update Amazon Athena workgroups by using the AWS CloudFormation `AWS::Athena::WorkGroup` resource\. For more information, see [AWS::Athena::WorkGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-workgroup.html) in the *AWS CloudFormation User Guide*\.

## Athena release notes for 2019<a name="release-notes-2019"></a>

### November 26, 2019<a name="release-note-2019-11-26"></a>

Published on 2019\-12\-17

Amazon Athena adds support for running SQL queries across relational, non\-relational, object, and custom data sources, invoking machine learning models in SQL queries, User Defined Functions \(UDFs\) \(Preview\), using Apache Hive Metastore as a metadata catalog with Amazon Athena \(Preview\), and four additional query\-related metrics\.

#### Federated SQL Queries<a name="release-note-2019-11-26-federated"></a>

Use Federated SQL queries to run SQL queries across relational, non\-relational, object, and custom data sources\.

You can now use Athena's federated query to scan data stored in relational, non\-relational, object, and custom data sources\. With federated querying, you can submit a single SQL query that scans data from multiple sources running on premises or hosted in the cloud\.

Running analytics on data spread across applications can be complex and time consuming for the following reasons:
+ Data required for analytics is often spread across relational, key\-value, document, in\-memory, search, graph, object, time\-series and ledger data stores\.
+ To analyze data across these sources, analysts build complex pipelines to extract, transform, and load into a data warehouse so that the data can be queried\.
+ Accessing data from various sources requires learning new programming languages and data access constructs\.

Federated SQL queries in Athena eliminate this complexity by allowing users to query the data in\-place from wherever it resides\. Analysts can use familiar SQL constructs to `JOIN` data across multiple data sources for quick analysis, and store results in Amazon S3 for subsequent use\.

##### Data Source Connectors<a name="release-note-2019-11-26-federated-connectors"></a>

Athena processes federated queries using Athena Data Source Connectors that run on [AWS Lambda](https://aws.amazon.com/lambda/)\. Use these open sourced data source connectors to run federated SQL queries in Athena across [Amazon DynamoDB](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaDynamoDBConnector), [Apache HBase](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaHBaseConnector), [Amazon Document DB](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaDocumentDBConnector), [Amazon CloudWatch](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaCloudwatchConnector), [Amazon CloudWatch Metrics](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaCloudwatchMetricsConnector), and [JDBC](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaJdbcConnector)\-compliant relational databases such MySQL, and PostgreSQL under the Apache 2\.0 license\.

##### Custom Data Source Connectors<a name="release-note-2019-11-26-federated-connectors-custom"></a>

Using [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases), developers can build connectors to any data source to enable Athena to run SQL queries against that data source\. Athena Query Federation Connector extends the benefits of federated querying beyond AWS provided connectors\. Because connectors run on AWS Lambda, you do not have to manage infrastructure or plan for scaling to peak demands\.

##### Preview Availability<a name="release-note-2019-11-26-federated-availability"></a>

Athena federated query is available in preview in the US East \(N\. Virginia\) Region\.

##### Next Steps<a name="release-note-2019-11-26-federated-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more about the federated query feature, see [Using Amazon Athena Federated Query \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source.html)\.
+ To get started with using an existing connector, see [Deploying a Connector and Connecting to a Data Source](http://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source-lambda.html)\.
+ To learn how to build your own data source connector using the Athena Query Federation SDK, see [Example Athena Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.

#### Invoking Machine Learning Models in SQL Queries<a name="release-note-2019-11-26-machine-learning"></a>

You can now invoke machine learning models for inference directly from your Athena queries\. The ability to use machine learning models in SQL queries makes complex tasks such anomaly detection, customer cohort analysis, and sales predictions as simple as invoking a function in a SQL query\.

##### ML Models<a name="release-note-2019-11-26-machine-learning-models"></a>

You can use more than a dozen built\-in machine learning algorithms provided by [Amazon SageMaker](https://aws.amazon.com/sagemaker/), train your own models, or find and subscribe to model packages from [AWS Marketplace](https://aws.amazon.com/marketplace/) and deploy on [Amazon SageMaker Hosting Services](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-hosting.html)\. There is no additional setup required\. You can invoke these ML models in your SQL queries from the Athena console, [Athena APIs](https://docs.aws.amazon.com/athena/latest/APIReference/Welcome.html), and through Athena's [preview JDBC driver](https://docs.aws.amazon.com/athena/latest/ug/connect-with-jdbc.html)\.

##### Preview Availability<a name="release-note-2019-11-26-machine-learning-availability"></a>

Athena's ML functionality is available today in preview in the US East \(N\. Virginia\) Region\.

##### Next Steps<a name="release-note-2019-11-26-machine-learning-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more about the machine learning feature, see [Using Machine Learning \(ML\) with Amazon Athena \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/querying-mlmodel.html)\.

#### User Defined Functions \(UDFs\) \(Preview\)<a name="release-note-2019-11-26-user-defined-functions"></a>

You can now write custom scalar functions and invoke them in your Athena queries\. You can write your UDFs in Java using the [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases)\. When a UDF is used in a SQL query submitted to Athena, it is invoked and run on [AWS Lambda](https://aws.amazon.com/lambda/)\. UDFs can be used in both `SELECT` and `FILTER` clauses of a SQL query\. You can invoke multiple UDFs in the same query\.

##### Preview Availability<a name="release-note-2019-11-26-user-defined-functions-availability"></a>

Athena UDF functionality is available in Preview mode in the US East \(N\. Virginia\) Region\.

##### Next Steps<a name="release-note-2019-11-26-user-defined-functions-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more, see [Querying with User Defined Functions \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/querying-udf.html)\.
+ For example UDF implementations, see [Amazon Athena UDF Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-udfs) on GitHub\.
+ To learn how to write your own functions using the Athena Query Federation SDK, see [Creating and Deploying a UDF Using Lambda](https://docs.aws.amazon.com/athena/latest/ug/querying-udf.html#udf-creating-and-deploying)\.

#### Using Apache Hive Metastore as a Metacatalog with Amazon Athena \(Preview\)<a name="release-note-2019-11-26-hive-metastore"></a>

You can now connect Athena to one or more Apache Hive Metastores in addition to the AWS Glue Data Catalog with Athena\.

##### Metastore Connector<a name="release-note-2019-11-26-hive-metastore-connector"></a>

To connect to a self\-hosted Hive Metastore, you need an Athena Hive Metastore connector\. Athena provides a [reference](https://s3.console.aws.amazon.com/s3/buckets/athena-downloads/preview-only/?region=us-east-1&tab=overview) implementation connector that you can use\. The connector runs as an AWS Lambda function in your account\. For more information, see [Using Athena Data Connector for External Hive Metastore \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/connect-to-data-source-hive.html)\.

##### Preview Availability<a name="release-note-2019-11-26-hive-metastore-availability"></a>

The Hive Metastore feature is available in Preview mode in the US East \(N\. Virginia\) Region\.

##### Next Steps<a name="release-note-2019-11-26-hive-metastore-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more about this feature, please visit our [Using Athena Data Connector for External Hive Metastore \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/connect-to-data-source-hive.html)\.

#### New Query\-Related Metrics<a name="release-note-2019-11-26-query-metrics"></a>

Athena now publishes additional query metrics that can help you understand [Amazon Athena](https://aws.amazon.com/athena/) performance\. Athena publishes query\-related metrics to [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/)\. In this release, Athena publishes the following additional query metrics:
+ **Query Planning Time** – The time taken to plan the query\. This includes the time spent retrieving table partitions from the data source\.
+ **Query Queuing Time** – The time that the query was in a queue waiting for resources\.
+ **Service Processing Time** – The time taken to write results after the query engine finishes processing\.
+ **Total Execution Time** – The time Athena took to run the query\.

To consume these new query metrics, you can create custom dashboards, set alarms and triggers on metrics in CloudWatch, or use pre\-populated dashboards directly from the Athena console\. 

##### Next Steps<a name="release-note-2019-11-26-query-metrics-next-steps"></a>

For more information, see [Monitoring Athena Queries with CloudWatch Metrics](https://docs.aws.amazon.com/athena/latest/ug/query-metrics-viewing.html)\.

### November 12, 2019<a name="release-note-2019-11-12"></a>

Published on 2019\-12\-17

Amazon Athena is now available in the Middle East \(Bahrain\) Region\.

### November 8, 2019<a name="release-note-2019-11-08"></a>

Published on 2019\-12\-17

Amazon Athena is now available in the US West \(N\. California\) Region and the Europe \(Paris\) Region\.

### October 8, 2019<a name="release-note-2019-10-08"></a>

Published on 2019\-12\-17

[Amazon Athena](https://aws.amazon.com/athena/) now allows you to connect directly to Athena through an interface VPC endpoint in your Virtual Private Cloud \(VPC\)\. Using this feature, you can submit your queries to Athena securely without requiring an Internet Gateway in your VPC\.

To create an interface VPC endpoint to connect to Athena, you can use the AWS Management Console or AWS Command Line Interface \(AWS CLI\)\. For information about creating an interface endpoint, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpce-interface.html#create-interface-endpoint)\.

When you use an interface VPC endpoint, communication between your VPC and Athena APIs is secure and stays within the AWS network\. There are no additional Athena costs to use this feature\. Interface VPC endpoint [charges](https://aws.amazon.com/privatelink/pricing/) apply\.

To learn more about this feature, see [Connect to Amazon Athena Using an Interface VPC Endpoint](https://docs.aws.amazon.com/athena/latest/ug/interface-vpc-endpoint.html)\.

### September 19, 2019<a name="release-note-2019-09-19"></a>

Published on 2019\-12\-17

Amazon Athena adds support for inserting new data to an existing table using the `INSERT INTO` statement\. You can insert new rows into a destination table based on a `SELECT` query statement that runs on a source table, or based on a set of values that are provided as part of the query statement\. Supported data formats include Avro, JSON, ORC, Parquet, and text files\.

`INSERT INTO` statements can also help you simplify your ETL process\. For example, you can use `INSERT INTO` in a single query to select data from a source table that is in JSON format and write to a destination table in Parquet format\.

`INSERT INTO` statements are charged based on the number of bytes that are scanned in the `SELECT` phase, similar to how Athena charges for `SELECT` queries\. For more information, see [Amazon Athena pricing](https://aws.amazon.com/athena/pricing/)\.

For more information about using `INSERT INTO`, including supported formats, SerDes and examples, see [INSERT INTO](https://docs.aws.amazon.com/athena/latest/ug/insert-into.html) in the Athena User Guide\.

### September 12, 2019<a name="release-note-2019-09-12"></a>

Published on 2019\-12\-17

Amazon Athena is now available in the Asia Pacific \(Hong Kong\) Region\.

### August 16, 2019<a name="release-note-2019-08-16"></a>

Published on 2019\-12\-17

[Amazon Athena](https://aws.amazon.com/athena/) adds support for querying data in Amazon S3 Requester Pays buckets\.

When an Amazon S3 bucket is configured as Requester Pays, the requester, not the bucket owner, pays for the Amazon S3 request and data transfer costs\. In Athena, workgroup administrators can now configure workgroup settings to allow workgroup members to query S3 Requester Pays buckets\.

For information about how to configure the Requester Pays setting for your workgroup, refer to [Create a Workgroup](https://docs.aws.amazon.com/athena/latest/ug/workgroups-create-update-delete.html#creating-workgroups) in the Amazon Athena User Guide\. For more information about Requester Pays buckets, see [Requester Pays Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/RequesterPaysBuckets.html) in the Amazon Simple Storage Service Developer Guide\.

### August 9, 2019<a name="release-note-2019-08-09"></a>

Published on 2019\-12\-17

Amazon Athena now supports enforcing [AWS Lake Formation](https://aws.amazon.com/lake-formation/) policies for fine\-grained access control to new or existing databases, tables, and columns defined in the [AWS Glue Data Catalog](https://aws.amazon.com/glue/) for data stored in Amazon S3\.

You can use this feature in the following AWS Regions: US East \(Ohio\), US East \(N\. Virginia\), US West \(Oregon\), Asia Pacific \(Tokyo\), and Europe \(Ireland\)\. There are no additional charges to use this feature\.

For more information about using this feature, see [Using Athena to query data registered with AWS Lake Formation](security-athena-lake-formation.md)\. For more information about AWS Lake Formation, see [AWS Lake Formation](https://aws.amazon.com/lake-formation/)\.

### June 26, 2019<a name="release-note-2019-06-26"></a>

Amazon Athena is now available in the Europe \(Stockholm\) Region\. For a list of supported Regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

### May 24, 2019<a name="release-note-2019-05-24"></a>

Published on *2019\-05\-24*

Amazon Athena is now available in the AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\) Regions\. For a list of supported Regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

### March 05, 2019<a name="release-note-2019-03-05"></a>

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
+ [Using workgroups for running queries](workgroups.md) and [Workgroup example policies](example-policies-workgroup.md)\.
+ [Tagging Athena resources](tags.md) and [Tag\-based IAM access control policies](tags-access-control.md)\.

If you use the JDBC driver or the AWS SDK, upgrade to the latest version of the driver and SDK, both of which already include support for workgroups and tags in Athena\. For more information, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### February 22, 2019<a name="release-note-2019-02-22"></a>

Published on *2019\-02\-22*

Added tag support for workgroups in Amazon Athena\. A tag consists of a key and a value, both of which you define\. When you tag a workgroup, you assign custom metadata to it\. You can add tags to workgroups to help categorize them, using [AWS tagging best practices](https://d1.awsstatic.com/whitepapers/aws-tagging-best-practices.pdf)\. You can use tags to restrict access to workgroups, and to track costs\. For example, create a workgroup for each cost center\. Then, by adding tags to these workgroups, you can track your Athena spending for each cost center\. For more information, see [Using Tags for Billing](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/custom-tags.html) in the *AWS Billing and Cost Management User Guide*\.

You can work with tags by using the Athena console or the API operations\. For more information, see [Tagging Athena resources](tags.md)\.

In the Athena console, you can add one or more tags to each of your workgroups, and search by tags\. Workgroups are an IAM\-controlled resource in Athena\. In IAM, you can restrict who can add, remove, or list tags on workgroups that you create\. You can also use the `CreateWorkGroup` API operation that has the optional tag parameter for adding one or more tags to the workgroup\. To add, remove, or list tags, use `TagResource`, `UntagResource`, and `ListTagsForResource`\. For more information, see [Using tag operations](tags-operations.md)\.

To allow users to add tags when creating workgroups, ensure that you give each user IAM permissions to both the `TagResource` and `CreateWorkGroup` API actions\. For more information and examples, see [Tag\-based IAM access control policies](tags-access-control.md)\. 

There are no changes to the JDBC driver when you use tags on workgroups\. If you create new workgroups and use the JDBC driver or the AWS SDK, upgrade to the latest version of the driver and SDK\. For information, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

### February 18, 2019<a name="release-note-2019-02-18"></a>

Published on *2019\-02\-18*

Added ability to control query costs by running queries in workgroups\. For information, see [Using workgroups to control query access and costs](manage-queries-control-costs-with-workgroups.md)\. Improved the JSON OpenX SerDe used in Athena, fixed an issue where Athena did not ignore objects transitioned to the `GLACIER` storage class, and added examples for querying Network Load Balancer logs\.

Made the following changes:
+ Added support for workgroups\. Use workgroups to separate users, teams, applications, or workloads, and to set limits on amount of data each query or the entire workgroup can process\. Because workgroups act as IAM resources, you can use resource\-level permissions to control access to a specific workgroup\. You can also view query\-related metrics in Amazon CloudWatch, control query costs by configuring limits on the amount of data scanned, create thresholds, and trigger actions, such as Amazon SNS alarms, when these thresholds are breached\. For more information, see [Using workgroups for running queries](workgroups.md) and [Controlling costs and monitoring queries with CloudWatch metrics and events](control-limits.md)\.

  Workgroups are an IAM resource\. For a full list of workgroup\-related actions, resources, and conditions in IAM, see [Actions, Resources, and Condition Keys for Amazon Athena](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonathena.html) in the *Service Authorization Reference*\. Before you create new workgroups, make sure that you use [workgroup IAM policies](workgroups-iam-policy.md), and the [AWS managed policy: AmazonAthenaFullAccess](managed-policies.md#amazonathenafullaccess-managed-policy)\. 

  You can start using workgroups in the console, with the [workgroup API operations](workgroups-api-list.md), or with the JDBC driver\. For a high\-level procedure, see [Setting up workgroups](workgroups-procedure.md)\. To download the JDBC driver with workgroup support, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

  If you use workgroups with the JDBC driver, you must set the workgroup name in the connection string using the `Workgroup` configuration parameter as in the following example:

  ```
  jdbc:awsathena://AwsRegion=<AWSREGION>;UID=<ACCESSKEY>;
  PWD=<SECRETKEY>;S3OutputLocation=s3://<athena-output>-<AWSREGION>/;
  Workgroup=<WORKGROUPNAME>;
  ```

  There are no changes in the way you run SQL statements or make JDBC API calls to the driver\. The driver passes the workgroup name to Athena\.

  For information about differences introduced with workgroups, see [ Athena workgroup APIs](workgroups-api-list.md) and [Troubleshooting workgroups](workgroups-troubleshooting.md)\.
+ Improved the JSON OpenX SerDe used in Athena\. The improvements include, but are not limited to, the following:
  + Support for the `ConvertDotsInJsonKeysToUnderscores` property\. When set to `TRUE`, it allows the SerDe to replace the dots in key names with underscores\. For example, if the JSON dataset contains a key with the name `"a.b"`, you can use this property to define the column name to be `"a_b"` in Athena\. The default is `FALSE`\. By default, Athena does not allow dots in column names\.
  + Support for the `case.insensitive` property\. By default, Athena requires that all keys in your JSON dataset use lowercase\. Using `WITH SERDE PROPERTIES ("case.insensitive"= FALSE;)` allows you to use case\-sensitive key names in your data\. The default is `TRUE`\. When set to `TRUE`, the SerDe converts all uppercase columns to lowercase\. 

  For more information, see [OpenX JSON SerDe](openx-json-serde.md)\.
+ Fixed an issue where Athena returned `"access denied"` error messages, when it processed Amazon S3 objects that were archived to Glacier by Amazon S3 lifecycle policies\. As a result of fixing this issue, Athena ignores objects transitioned to the `GLACIER` storage class\. Athena does not support querying data from the `GLACIER` storage class\. 

   For more information, see [Requirements for tables in Athena and data in Amazon S3](creating-tables.md#s3-considerations) and [Transitioning to the GLACIER Storage Class \(Object Archival\) ](https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-transition-general-considerations.html#before-deciding-to-archive-objects) in the *Amazon Simple Storage Service User Guide*\.
+ Added examples for querying Network Load Balancer access logs that receive information about the Transport Layer Security \(TLS\) requests\. For more information, see [Querying Network Load Balancer logs](networkloadbalancer-classic-logs.md)\.

## Athena release notes for 2018<a name="release-notes-2018"></a>

### November 20, 2018<a name="release-note-2018-11-20"></a>

Published on *2018\-11\-20*

Released the new versions of the JDBC and ODBC driver with support for federated access to Athena API with the AD FS and SAML 2\.0 \(Security Assertion Markup Language 2\.0\)\. For details, see the [JDBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.6/docs/release-notes.txt) and [ODBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/release-notes.txt)\.

With this release, federated access to Athena is supported for the Active Directory Federation Service \(AD FS 3\.0\)\. Access is established through the versions of JDBC or ODBC drivers that support SAML 2\.0\. For information about configuring federated access to the Athena API, see [Enabling federated access to the Athena API](access-federation-saml.md)\.

To download the JDBC driver version 2\.0\.6 and its documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\. For information about this version, see [JDBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.6/docs/release-notes.txt)\.

To download the ODBC driver version 1\.0\.4 and its documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\. For information about this version, [ODBC Driver Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/release-notes.txt)\. 

For more information about SAML 2\.0 support in AWS, see [About SAML 2\.0 Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html) in the *IAM User Guide*\. 

### October 15, 2018<a name="release-note-2018-10-15"></a>

Published on *2018\-10\-15*

If you have upgraded to the AWS Glue Data Catalog, there are two new features that provide support for: 
+ Encryption of the Data Catalog metadata\. If you choose to encrypt metadata in the Data Catalog, you must add specific policies to Athena\. For more information, see [Access to Encrypted Metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.
+ Fine\-grained permissions to access resources in the AWS Glue Data Catalog\. You can now define identity\-based \(IAM\) policies that restrict or allow access to specific databases and tables from the Data Catalog used in Athena\. For more information, see [Fine\-grained access to databases and tables in the AWS Glue Data Catalog](fine-grained-access-to-glue-resources.md)\.
**Note**  
Data resides in the Amazon S3 buckets, and access to it is governed by the [Access to Amazon S3](s3-permissions.md)\. To access data in databases and tables, continue to use access control policies to Amazon S3 buckets that store the data\. 

### October 10, 2018<a name="release-note-2018-10-10"></a>

Published on *2018\-10\-10*

Athena supports `CREATE TABLE AS SELECT`, which creates a table from the result of a `SELECT` query statement\. For details, see [Creating a Table from Query Results \(CTAS\)](https://docs.aws.amazon.com/athena/latest/ug/ctas.html)\.

Before you create CTAS queries, it is important to learn about their behavior in the Athena documentation\. It contains information about the location for saving query results in Amazon S3, the list of supported formats for storing CTAS query results, the number of partitions you can create, and supported compression formats\. For more information, see [Considerations and limitations for CTAS queries](considerations-ctas.md)\.

Use CTAS queries to:
+ [Create a table from query results](ctas.md) in one step\.
+ [Create CTAS queries in the Athena console](ctas-console.md), using [Examples](ctas-examples.md)\. For information about syntax, see [CREATE TABLE AS](create-table-as.md)\.
+  Transform query results into other storage formats, such as PARQUET, ORC, AVRO, JSON, and TEXTFILE\. For more information, see [Considerations and limitations for CTAS queries](considerations-ctas.md) and [Columnar storage formats](columnar-storage.md)\.

### September 6, 2018<a name="release-note-2018-09-06"></a>

Published on *2018\-09\-06*

Released the new version of the ODBC driver \(version 1\.0\.3\)\. The new version of the ODBC driver streams results by default, instead of paging through them, allowing business intelligence tools to retrieve large data sets faster\. This version also includes improvements, bug fixes, and an updated documentation for *"Using SSL with a Proxy Server"*\. For details, see the [Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/release-notes.txt) for the driver\.

For downloading the ODBC driver version 1\.0\.3 and its documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\. 

The streaming results feature is available with this new version of the ODBC driver\. It is also available with the JDBC driver\. For information about streaming results, see the [ ODBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf), and search for **UseResultsetStreaming**\.

The ODBC driver version 1\.0\.3 is a drop\-in replacement for the previous version of the driver\. We recommend that you migrate to the current driver\. 

**Important**  
To use the ODBC driver version 1\.0\.3, follow these requirements:   
Keep the port 444 open to outbound traffic\.
Add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API and is only used with the ODBC and JDBC drivers, as part of streaming results support\. For an example policy, see [AWS managed policy: AWSQuicksightAthenaAccess](managed-policies.md#awsquicksightathenaaccess-managed-policy)\. 

### August 23, 2018<a name="release-note-2018-08-23"></a>

Published on *2018\-08\-23*

Added support for these DDL\-related features and fixed several bugs, as follows: 
+ Added support for `BINARY` and `DATE` data types for data in Parquet, and for `DATE` and `TIMESTAMP` data types for data in Avro\. 
+ Added support for `INT` and `DOUBLE` in DDL queries\. `INTEGER` is an alias to `INT`, and `DOUBLE PRECISION` is an alias to `DOUBLE`\. 
+ Improved performance of `DROP TABLE` and `DROP DATABASE` queries\.
+ Removed the creation of `_$folder$` object in Amazon S3 when a data bucket is empty\.
+ Fixed an issue where `ALTER TABLE ADD PARTITION` threw an error when no partition value was provided\. 
+ Fixed an issue where `DROP TABLE` ignored the database name when checking partitions after the qualified name had been specified in the statement\. 

For more about the data types supported in Athena, see [Data types in Amazon Athena](data-types.md)\.

For information about supported data type mappings between types in Athena, the JDBC driver, and Java data types, see the *"Data Types"* section in the [JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf)\. 

### August 16, 2018<a name="release-note-2018-08-16"></a>

Published on *2018\-08\-16*

Released the JDBC driver version 2\.0\.5\. The new version of the JDBC driver streams results by default, instead of paging through them, allowing business intelligence tools to retrieve large data sets faster\. Compared to the previous version of the JDBC driver, there are the following performance improvements:
+ Approximately 2x performance increase when fetching less than 10K rows\.
+ Approximately 5\-6x performance increase when fetching more than 10K rows\. 

The streaming results feature is available only with the JDBC driver\. It is not available with the ODBC driver\. You cannot use it with the Athena API\. For information about streaming results, see the [ JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf), and search for **UseResultsetStreaming**\.

For downloading the JDBC driver version 2\.0\.5 and its documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\. 

The JDBC driver version 2\.0\.5 is a drop\-in replacement for the previous version of the driver \(2\.0\.2\)\. To ensure that you can use the JDBC driver version 2\.0\.5, add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API and is only used with the JDBC driver, as part of streaming results support\. For an example policy, see [AWS managed policy: AWSQuicksightAthenaAccess](managed-policies.md#awsquicksightathenaaccess-managed-policy)\. For more information about migrating from version 2\.0\.2 to version 2\.0\.5 of the driver, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. 

If you are migrating from a 1\.x driver to a 2\.x driver, you will need to migrate your existing configurations to the new configuration\. We highly recommend that you migrate to the current version of the driver\. For more information, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. 

### August 7, 2018<a name="release-note-2018-08-07"></a>

Published on *2018\-08\-07*

You can now store Amazon Virtual Private Cloud flow logs directly in Amazon S3 in a GZIP format, where you can query them in Athena\. For information, see [Querying Amazon VPC flow logs](vpc-flow-logs.md) and [ Amazon VPC Flow Logs can now be delivered to S3](https://aws.amazon.com/about-aws/whats-new/2018/08/amazon-vpc-flow-logs-can-now-be-delivered-to-s3/)\.

### June 5, 2018<a name="release-note-2018-06-05"></a>

Published on *2018\-06\-05*

**Topics**
+ [Support for Views](#support-for-views)
+ [Improvements and Updates to Error Messages](#improvements)
+ [Bug Fixes](#bug-fixes)

#### Support for Views<a name="support-for-views"></a>

Added support for views\. You can now use [CREATE VIEW](create-view.md), [DESCRIBE VIEW](describe-view.md), [DROP VIEW](drop-view.md), [SHOW CREATE VIEW](show-create-view.md), and [SHOW VIEWS](show-views.md) in Athena\. The query that defines the view runs each time you reference the view in your query\. For more information, see [Working with views](views.md)\.

#### Improvements and Updates to Error Messages<a name="improvements"></a>
+ Included a GSON 2\.8\.0 library into the CloudTrail SerDe, to solve an issue with the CloudTrail SerDe and enable parsing of JSON strings\.
+ Enhanced partition schema validation in Athena for Parquet, and, in some cases, for ORC, by allowing reordering of columns\. This enables Athena to better deal with changes in schema evolution over time, and with tables added by the AWS Glue Crawler\. For more information, see [Handling schema updates](handling-schema-updates-chapter.md)\.
+ Added parsing support for `SHOW VIEWS`\.
+ Made the following improvements to most common error messages: 
  + Replaced an Internal Error message with a descriptive error message when a SerDe fails to parse the column in an Athena query\. Previously, Athena issued an internal error in cases of parsing errors\. The new error message reads: "HIVE\_BAD\_DATA: Error parsing field value for field 0: java\.lang\.String cannot be cast to org\.openx\.data\.jsonserde\.json\.JSONObject"\.
  + Improved error messages about insufficient permissions by adding more detail\.

#### Bug Fixes<a name="bug-fixes"></a>

Fixed the following bugs:
+ Fixed an issue that enables the internal translation of `REAL` to `FLOAT` data types\. This improves integration with the AWS Glue crawler that returns `FLOAT` data types\. 
+ Fixed an issue where Athena was not converting AVRO `DECIMAL` \(a logical type\) to a `DECIMAL` type\. 
+ Fixed an issue where Athena did not return results for queries on Parquet data with `WHERE` clauses that referenced values in the `TIMESTAMP` data type\.

### May 17, 2018<a name="release-note-2018-05-17"></a>

Published on *2018\-05\-17*

Increased query concurrency quota in Athena from five to twenty\. This means that you can submit and run up to twenty `DDL` queries and twenty `SELECT` queries at a time\. Note that the concurrency quotas are separate for `DDL` and `SELECT` queries\. 

Concurrency quotas in Athena are defined as the number of queries that can be submitted to the service concurrently\. You can submit up to twenty queries of the same type \(`DDL` or `SELECT`\) at a time\. If you submit a query that exceeds the concurrent query quota, the Athena API displays an error message\.

After you submit your queries to Athena, it processes the queries by assigning resources based on the overall service load and the amount of incoming requests\. We continuously monitor and make adjustments to the service so that your queries process as fast as possible\. 

For information, see [Service Quotas](service-limits.md)\. This is an adjustable quota\. You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/athena/quotas) to request a quota increase for concurrent queries\.

### April 19, 2018<a name="release-note-2018-04-19"></a>

Published on *2018\-04\-19*

Released the new version of the JDBC driver \(version 2\.0\.2\) with support for returning the `ResultSet` data as an Array data type, improvements, and bug fixes\. For details, see the [Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/release-notes.txt) for the driver\.

For information about downloading the new JDBC driver version 2\.0\.2 and its documentation, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.

The latest version of the JDBC driver is 2\.0\.2\. If you are migrating from a 1\.x driver to a 2\.x driver, you will need to migrate your existing configurations to the new configuration\. We highly recommend that you migrate to the current driver\. 

For information about the changes introduced in the new version of the driver, the version differences, and examples, see the [JDBC Driver Migration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/Simba+Athena+JDBC+Driver+Migration+Guide.pdf)\. 

### April 6, 2018<a name="release-note-2018-04-06"></a>

Published on *2018\-04\-06*

Use auto\-complete to type queries in the Athena console\. 

### March 15, 2018<a name="release-note-2018-03-15"></a>

Published on *2018\-03\-15*

 Added an ability to automatically create Athena tables for CloudTrail log files directly from the CloudTrail console\. For information, see [Using the CloudTrail console to create an Athena table for CloudTrail logs ](cloudtrail-logs.md#create-cloudtrail-table-ct)\. 

### February 2, 2018<a name="release-note-2018-02-12"></a>

Published on *2018\-02\-12*

Added an ability to securely offload intermediate data to disk for memory\-intensive queries that use the `GROUP BY` clause\. This improves the reliability of such queries, preventing "Query resource exhausted" errors\.

### January 19, 2018<a name="release-note-2018-01-19"></a>

Published on *2018\-01\-19*

Athena uses Presto, an open\-source distributed query engine, to run queries\. 

With Athena, there are no versions to manage\. We have transparently upgraded the underlying engine in Athena to a version based on Presto version 0\.172\. No action is required on your end\. 

With the upgrade, you can now use [Presto 0\.172 Functions and Operators](https://prestodb.io/docs/0.172/functions.html), including [Presto 0\.172 Lambda Expressions](https://prestodb.io/docs/0.172/functions/lambda.html) in Athena\. 

Major updates for this release, including the community\-contributed fixes, include:
+ Support for ignoring headers\. You can use the `skip.header.line.count` property when defining tables, to allow Athena to ignore headers\. This is supported for queries that use the [LazySimpleSerDe](lazy-simple-serde.md) and [OpenCSV SerDe](csv-serde.md), and not for Grok or Regex SerDes\.
+ Support for the `CHAR(n)` data type in `STRING` functions\. The range for `CHAR(n)` is `[1.255]`, while the range for `VARCHAR(n)` is `[1,65535]`\. 
+ Support for correlated subqueries\.
+ Support for Presto Lambda expressions and functions\.
+ Improved performance of the `DECIMAL` type and operators\.
+ Support for filtered aggregations, such as `SELECT sum(col_name) FILTER`, where `id > 0`\.
+ Push\-down predicates for the `DECIMAL`, `TINYINT`, `SMALLINT`, and `REAL` data types\.
+ Support for quantified comparison predicates: `ALL`, `ANY`, and `SOME`\. 
+ Added functions: [https://prestodb.io/docs/0.172/functions/array.html#arrays_overlap](https://prestodb.io/docs/0.172/functions/array.html#arrays_overlap), [https://prestodb.io/docs/0.172/functions/array.html#array_except](https://prestodb.io/docs/0.172/functions/array.html#array_except), [https://prestodb.io/docs/0.172/functions/string.html#levenshtein_distance](https://prestodb.io/docs/0.172/functions/string.html#levenshtein_distance), [https://prestodb.io/docs/0.172/functions/string.html#codepoint](https://prestodb.io/docs/0.172/functions/string.html#codepoint), [https://prestodb.io/docs/0.172/functions/aggregate.html#skewness](https://prestodb.io/docs/0.172/functions/aggregate.html#skewness), [https://prestodb.io/docs/0.172/functions/aggregate.html#kurtosis](https://prestodb.io/docs/0.172/functions/aggregate.html#kurtosis), and [https://prestodb.io/docs/0.172/functions/conversion.html#typeof](https://prestodb.io/docs/0.172/functions/conversion.html#typeof)\.
+ Added a variant of the [https://prestodb.io/docs/0.172/functions/datetime.html#from_unixtime](https://prestodb.io/docs/0.172/functions/datetime.html#from_unixtime) function that takes a timezone argument\.
+ Added the [https://prestodb.io/docs/0.172/functions/aggregate.html#bitwise_and_agg](https://prestodb.io/docs/0.172/functions/aggregate.html#bitwise_and_agg) and [https://prestodb.io/docs/0.172/functions/aggregate.html#bitwise_or_agg](https://prestodb.io/docs/0.172/functions/aggregate.html#bitwise_or_agg) aggregation functions\.
+  Added the [https://prestodb.io/docs/0.172/functions/binary.html#xxhash64](https://prestodb.io/docs/0.172/functions/binary.html#xxhash64) and [https://prestodb.io/docs/0.172/functions/binary.html#to_big_endian_64](https://prestodb.io/docs/0.172/functions/binary.html#to_big_endian_64) functions\. 
+ Added support for escaping double quotes or backslashes using a backslash with a JSON path subscript to the [https://prestodb.io/docs/0.172/functions/json.html#json_extract](https://prestodb.io/docs/0.172/functions/json.html#json_extract) and [https://prestodb.io/docs/0.172/functions/json.html#json_extract_scalar](https://prestodb.io/docs/0.172/functions/json.html#json_extract_scalar) functions\. This changes the semantics of any invocation using a backslash, as backslashes were previously treated as normal characters\.

For a complete list of functions and operators, see [DML queries, functions, and operators](functions-operators-reference-section.md) in this guide, and [Presto 0\.172 Functions](https://prestodb.io/docs/0.172/functions.html)\. 

Athena does not support all of Presto's features\. For more information, see [Limitations](other-notable-limitations.md)\.

## Athena release notes for 2017<a name="release-notes-2017"></a>

### November 13, 2017<a name="release-note-2017-11-13"></a>

Published on *2017\-11\-13*

Added support for connecting Athena to the ODBC Driver\. For information, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

### November 1, 2017<a name="release-note-2017-11-01"></a>

Published on *2017\-11\-01*

Added support for querying geospatial data, and for Asia Pacific \(Seoul\), Asia Pacific \(Mumbai\), and EU \(London\) regions\. For information, see [Querying geospatial data](querying-geospatial-data.md) and [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

### October 19, 2017<a name="release-note-2017-10-19"></a>

Published on *2017\-10\-19*

Added support for EU \(Frankfurt\)\. For a list of supported regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

### October 3, 2017<a name="release-note-2017-10-03"></a>

Published on *2017\-10\-03*

Create named Athena queries with AWS CloudFormation\. For more information, see [AWS::Athena::NamedQuery](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-namedquery.html) in the *AWS CloudFormation User Guide*\.

### September 25, 2017<a name="release-note-2017-09-25"></a>

Published on *2017\-09\-25*

Added support for Asia Pacific \(Sydney\)\. For a list of supported regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

### August 14, 2017<a name="release-note-2017-08-14"></a>

Published on *2017\-08\-14*

Added integration with the AWS Glue Data Catalog and a migration wizard for updating from the Athena managed data catalog to the AWS Glue Data Catalog\. For more information, see [Integration with AWS Glue](glue-athena.md)\.

### August 4, 2017<a name="release-note-2017-08-04"></a>

Published on *2017\-08\-04*

Added support for Grok SerDe, which provides easier pattern matching for records in unstructured text files such as logs\. For more information, see [Grok SerDe](grok-serde.md)\. Added keyboard shortcuts to scroll through query history using the console \(CTRL \+ ⇧/⇩ using Windows, CMD \+ ⇧/⇩ using Mac\)\.

### June 22, 2017<a name="release-note-2017-06-22"></a>

Published on *2017\-06\-22*

Added support for Asia Pacific \(Tokyo\) and Asia Pacific \(Singapore\)\. For a list of supported regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

### June 8, 2017<a name="release-note-2017-06-08"></a>

Published on *2017\-06\-08*

Added support for Europe \(Ireland\)\. For more information, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

### May 19, 2017<a name="release-note-2017-05-19"></a>

Published on *2017\-05\-19*

Added an Amazon Athena API and AWS CLI support for Athena; updated JDBC driver to version 1\.1\.0; fixed various issues\.
+ Amazon Athena enables application programming for Athena\. For more information, see [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. The latest AWS SDKs include support for the Athena API\. For links to documentation and downloads, see the *SDKs* section in [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\. 
+  The AWS CLI includes new commands for Athena\. For more information, see the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. 
+  A new JDBC driver 1\.1\.0 is available, which supports the new Athena API as well as the latest features and bug fixes\. Download the driver at [ https://s3\.amazonaws\.com/athena\-downloads/drivers/AthenaJDBC41\-1\.1\.0\.jar](https://s3.amazonaws.com/athena-downloads/drivers/AthenaJDBC41-1.1.0.jar)\. We recommend upgrading to the latest Athena JDBC driver; however, you may still use the earlier driver version\. Earlier driver versions do not support the Athena API\. For more information, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.
+ Actions specific to policy statements in earlier versions of Athena have been deprecated\. If you upgrade to JDBC driver version 1\.1\.0 and have customer\-managed or inline IAM policies attached to JDBC users, you must update the IAM policies\. In contrast, earlier versions of the JDBC driver do not support the Athena API, so you can specify only deprecated actions in policies attached to earlier version JDBC users\. For this reason, you shouldn't need to update customer\-managed or inline IAM policies\.
+ These policy\-specific actions were used in Athena before the release of the Athena API\. Use these deprecated actions in policies **only** with JDBC drivers earlier than version 1\.1\.0\. If you are upgrading the JDBC driver, replace policy statements that allow or deny deprecated actions with the appropriate API actions as listed or errors will occur:


| Deprecated Policy\-Specific Action | Corresponding Athena API Action | 
| --- |--- |
|  <pre>athena:RunQuery</pre>  |  <pre>athena:StartQueryExecution</pre>  | 
|  <pre>athena:CancelQueryExecution</pre>  |  <pre>athena:StopQueryExecution</pre>  | 
|  <pre>athena:GetQueryExecutions</pre>  |  <pre>athena:ListQueryExecutions</pre>  | 

#### Improvements<a name="release-note-2017-05-19-improvements"></a>
+ Increased the query string length limit to 256 KB\.

#### Bug Fixes<a name="release-note-2017-05-19-bug-fixes"></a>
+ Fixed an issue that caused query results to look malformed when scrolling through results in the console\.
+  Fixed an issue where a `\u0000` character string in Amazon S3 data files would cause errors\. 
+ Fixed an issue that caused requests to cancel a query made through the JDBC driver to fail\.
+ Fixed an issue that caused the AWS CloudTrail SerDe to fail with Amazon S3 data in US East \(Ohio\)\.
+ Fixed an issue that caused `DROP TABLE` to fail on a partitioned table\.

### April 4, 2017<a name="release-note-2017-04-04"></a>

Published on *2017\-04\-04*

Added support for Amazon S3 data encryption and released JDBC driver update \(version 1\.0\.1\) with encryption support, improvements, and bug fixes\.

#### Features<a name="release-note-2017-04-04-features"></a>
+ Added the following encryption features:
  +  Support for querying encrypted data in Amazon S3\. 
  +  Support for encrypting Athena query results\. 
+ A new version of the driver supports new encryption features, adds improvements, and fixes issues\.
+ Added the ability to add, replace, and change columns using `ALTER TABLE`\. For more information, see [Alter Column](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterColumn) in the Hive documentation\.
+ Added support for querying LZO\-compressed data\.

For more information, see [Encryption at rest](encryption.md)\.

#### Improvements<a name="release-note-2017-04-04-improvements"></a>
+ Better JDBC query performance with page\-size improvements, returning 1,000 rows instead of 100\.
+ Added ability to cancel a query using the JDBC driver interface\.
+ Added ability to specify JDBC options in the JDBC connection URL\. See [Using Athena with the JDBC driver](connect-with-jdbc.md) for the most current JDBC driver\.
+ Added PROXY setting in the driver, which can now be set using [ClientConfiguration](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/ClientConfiguration.html) in the AWS SDK for Java\.

#### Bug Fixes<a name="release-note-2017-04-04-bug-fixes"></a>

Fixed the following bugs:
+ Throttling errors would occur when multiple queries were issued using the JDBC driver interface\.
+ The JDBC driver would stop when projecting a decimal data type\.
+  The JDBC driver would return every data type as a string, regardless of how the data type was defined in the table\. For example, selecting a column defined as an `INT` data type using `resultSet.GetObject()` would return a `STRING` data type instead of `INT`\. 
+ The JDBC driver would verify credentials at the time a connection was made, rather than at the time a query would run\.
+ Queries made through the JDBC driver would fail when a schema was specified along with the URL\.

### March 24, 2017<a name="release-note-2017-03-24"></a>

Published on *2017\-03\-24*

Added the AWS CloudTrail SerDe, improved performance, fixed partition issues\.

#### Features<a name="release-note-2017-03-24-features"></a>
+ Added the AWS CloudTrail SerDe\. For more information, see [CloudTrail SerDe](cloudtrail-serde.md)\. For detailed usage examples, see the AWS Big Data Blog post, [ Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

#### Improvements<a name="release-note-2017-03-24-improvements"></a>
+ Improved performance when scanning a large number of partitions\.
+ Improved performance on `MSCK Repair Table` operation\.
+ Added ability to query Amazon S3 data stored in regions other than your primary Region\. Standard inter\-region data transfer rates for Amazon S3 apply in addition to standard Athena charges\.

#### Bug Fixes<a name="release-note-2017-03-24-bug-fixes"></a>
+ Fixed a bug where a "table not found error" might occur if no partitions are loaded\.
+ Fixed a bug to avoid throwing an exception with `ALTER TABLE ADD PARTITION IF NOT EXISTS` queries\.
+ Fixed a bug in `DROP PARTITIONS`\.

### February 20, 2017<a name="release-note-2017-02-20"></a>

Published on *2017\-02\-20*

Added support for AvroSerDe and OpenCSVSerDe, US East \(Ohio\) Region, and bulk editing columns in the console wizard\. Improved performance on large Parquet tables\.

#### Features<a name="release-note-2017-02-20-features"></a>
+ **Introduced support for new SerDes:**
  + [Avro SerDe](avro-serde.md)
  + [OpenCSVSerDe for processing CSV](csv-serde.md)
+ **US East \(Ohio\)** Region \(**us\-east\-2**\) launch\. You can now run queries in this region\.
+ You can now use the **Create Table From S3 bucket data** form to define table schema in bulk\. In the query editor, choose **Create**, **S3 bucket data**, and then choose **Bulk add columns** in the **Column details** section\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/bulk-add.png)

  Type name value pairs in the text box and choose **Add**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/bulk_column.png)

#### Improvements<a name="release-note-2017-02-20-improvements"></a>
+ Improved performance on large Parquet tables\.