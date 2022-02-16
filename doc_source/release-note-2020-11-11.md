# November 11, 2020<a name="release-note-2020-11-11"></a>

Published on 2020\-11\-11

Amazon Athena announces general availability in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions for Athena engine version 2 and federated queries\.

## Athena engine version 2<a name="release-note-2020-11-11-engine-version-2"></a>

Amazon Athena announces general availability of a new query engine version, Athena engine version 2, in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions\.

Athena engine version 2 includes performance enhancements and new feature capabilities such as schema evolution support for Parquet format data, additional geospatial functions, support for reading nested schema to reduce cost, and performance enhancements in JOIN and AGGREGATE operations\.
+ For information about improvements, breaking changes, and bug fixes, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.
+ For information about how to upgrade, see [Changing Athena Engine Versions](engine-versions-changing.md)\.
+ For information about testing queries, see [Testing Queries in Advance of an Engine Version Upgrade](engine-versions-changing.md#engine-versions-testing)\.

## Federated SQL Queries<a name="release-note-2020-11-11-federated"></a>

You can now use Athena’s federated query in the in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions without using the `AmazonAthenaPreviewFunctionality` workgroup\. 

Use Federated SQL queries to run SQL queries across relational, non\-relational, object, and custom data sources\. With federated querying, you can submit a single SQL query that scans data from multiple sources running on premises or hosted in the cloud\.

Running analytics on data spread across applications can be complex and time consuming for the following reasons:
+ Data required for analytics is often spread across relational, key\-value, document, in\-memory, search, graph, object, time\-series and ledger data stores\.
+ To analyze data across these sources, analysts build complex pipelines to extract, transform, and load into a data warehouse so that the data can be queried\.
+ Accessing data from various sources requires learning new programming languages and data access constructs\.

Federated SQL queries in Athena eliminate this complexity by allowing users to query the data in\-place from wherever it resides\. Analysts can use familiar SQL constructs to `JOIN` data across multiple data sources for quick analysis, and store results in Amazon S3 for subsequent use\.

### Data Source Connectors<a name="release-note-2020-11-11-federated-connectors"></a>

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

#### Custom Data Source Connectors<a name="release-note-2020-11-11-federated-connectors-custom"></a>

Using [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases), developers can build connectors to any data source to enable Athena to run SQL queries against that data source\. Athena Query Federation Connector extends the benefits of federated querying beyond AWS provided connectors\. Because connectors run on AWS Lambda, you do not have to manage infrastructure or plan for scaling to peak demands\.

### Next Steps<a name="release-note-2020-11-11-federated-next-steps"></a>
+ To learn more about the federated query feature, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md)\.
+ To get started with using an existing connector, see [Deploying a Connector and Connecting to a Data Source](http://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source-lambda.html)\.
+ To learn how to build your own data source connector using the Athena Query Federation SDK, see [Example Athena Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.