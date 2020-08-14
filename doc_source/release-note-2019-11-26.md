# November 26, 2019<a name="release-note-2019-11-26"></a>

Published on 2019\-12\-17

Amazon Athena adds support for running SQL queries across relational, non\-relational, object, and custom data sources, invoking machine learning models in SQL queries, User Defined Functions \(UDFs\) \(Preview\), using Apache Hive Metastore as a metadata catalog with Amazon Athena \(Preview\), and four additional query\-related metrics\.

## Federated SQL Queries<a name="release-note-2019-11-26-federated"></a>

Use Federated SQL queries to run SQL queries across relational, non\-relational, object, and custom data sources\.

You can now use Athena’s federated query to scan data stored in relational, non\-relational, object, and custom data sources\. With federated querying, you can submit a single SQL query that scans data from multiple sources running on premises or hosted in the cloud\.

Running analytics on data spread across applications can be complex and time consuming for the following reasons:
+ Data required for analytics is often spread across relational, key\-value, document, in\-memory, search, graph, object, time\-series and ledger data stores\.
+ To analyze data across these sources, analysts build complex pipelines to extract, transform, and load into a data warehouse so that the data can be queried\.
+ Accessing data from various sources requires learning new programming languages and data access constructs\.

Federated SQL queries in Athena eliminate this complexity by allowing users to query the data in\-place from wherever it resides\. Analysts can use familiar SQL constructs to `JOIN` data across multiple data sources for quick analysis, and store results in Amazon S3 for subsequent use\.

### Data Source Connectors<a name="release-note-2019-11-26-federated-connectors"></a>

Athena processes federated queries using Athena Data Source Connectors that run on [AWS Lambda](https://aws.amazon.com/lambda/)\. Use these open sourced data source connectors to run federated SQL queries in Athena across [Amazon DynamoDB](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaDynamoDBConnector), [Apache HBase](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaHBaseConnector), [Amazon Document DB](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaDocumentDBConnector), [Amazon Redshift](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaJdbcConnector), [Amazon CloudWatch](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaCloudwatchConnector), [Amazon CloudWatch Metrics](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaCloudwatchMetricsConnector), and [JDBC](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaJdbcConnector)\-compliant relational databases such MySQL, and PostgreSQL under the Apache 2\.0 license\.

#### Custom Data Source Connectors<a name="release-note-2019-11-26-federated-connectors-custom"></a>

Using [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases), developers can build connectors to any data source to enable Athena to run SQL queries against that data source\. Athena Query Federation Connector extends the benefits of federated querying beyond AWS provided connectors\. Because connectors run on AWS Lambda, you do not have to manage infrastructure or plan for scaling to peak demands\.

### Preview Availability<a name="release-note-2019-11-26-federated-availability"></a>

Athena federated query is available in preview in the US East \(N\. Virginia\) Region\.

### Next Steps<a name="release-note-2019-11-26-federated-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more about the federated query feature, see [Using Amazon Athena Federated Query \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source.html)\.
+ To get started with using an existing connector, see [Deploying a Connector and Connecting to a Data Source](http://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source-lambda.html)\.
+ To learn how to build your own data source connector using the Athena Query Federation SDK, see [Example Athena Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.

## Invoking Machine Learning Models in SQL Queries<a name="release-note-2019-11-26-machine-learning"></a>

You can now invoke machine learning models for inference directly from your Athena queries\. The ability to use machine learning models in SQL queries makes complex tasks such anomaly detection, customer cohort analysis, and sales predictions as simple as invoking a function in a SQL query\.

### ML Models<a name="release-note-2019-11-26-machine-learning-models"></a>

You can use more than a dozen built\-in machine learning algorithms provided by [Amazon SageMaker](https://aws.amazon.com/sagemaker/), train your own models, or find and subscribe to model packages from [AWS Marketplace](https://aws.amazon.com/marketplace/) and deploy on [Amazon SageMaker Hosting Services](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-hosting.html)\. There is no additional setup required\. You can invoke these ML models in your SQL queries from the Athena console, [Athena APIs](https://docs.aws.amazon.com/athena/latest/APIReference/Welcome.html), and through Athena’s [preview JDBC driver](https://docs.aws.amazon.com/athena/latest/ug/connect-with-jdbc.html)\.

### Preview Availability<a name="release-note-2019-11-26-machine-learning-availability"></a>

Athena’s ML functionality is available today in preview in the US East \(N\. Virginia\) Region\.

### Next Steps<a name="release-note-2019-11-26-machine-learning-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more about the machine learning feature, see [Using Machine Learning \(ML\) with Amazon Athena \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/querying-mlmodel.html)\.

## User Defined Functions \(UDFs\) \(Preview\)<a name="release-note-2019-11-26-user-defined-functions"></a>

You can now write custom scalar functions and invoke them in your Athena queries\. You can write your UDFs in Java using the [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases)\. When a UDF is used in a SQL query submitted to Athena, it is invoked and run on [AWS Lambda](https://aws.amazon.com/lambda/)\. UDFs can be used in both `SELECT` and `FILTER` clauses of a SQL query\. You can invoke multiple UDFs in the same query\.

### Preview Availability<a name="release-note-2019-11-26-user-defined-functions-availability"></a>

Athena UDF functionality is available in Preview mode in the US East \(N\. Virginia\) Region\.

### Next Steps<a name="release-note-2019-11-26-user-defined-functions-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more, see [Querying with User Defined Functions \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/querying-udf.html)\.
+ For example UDF implementations, see [Amazon Athena UDF Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-udfs) on GitHub\.
+ To learn how to write your own functions using the Athena Query Federation SDK, see [Creating and Deploying a UDF Using Lambda](https://docs.aws.amazon.com/athena/latest/ug/querying-udf.html#udf-creating-and-deploying)\.

## Using Apache Hive Metastore as a Metacatalog with Amazon Athena \(Preview\)<a name="release-note-2019-11-26-hive-metastore"></a>

You can now connect Athena to one or more Apache Hive Metastores in addition to the AWS Glue Data Catalog with Athena\.

### Metastore Connector<a name="release-note-2019-11-26-hive-metastore-connector"></a>

To connect to a self\-hosted Hive Metastore, you need an Athena Hive Metastore connector\. Athena provides a [reference](https://s3.console.aws.amazon.com/s3/buckets/athena-downloads/preview-only/?region=us-east-1&tab=overview) implementation connector that you can use\. The connector runs as an AWS Lambda function in your account\. For more information, see [Using Athena Data Connector for External Hive Metastore \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/connect-to-data-source-hive.html)\.

### Preview Availability<a name="release-note-2019-11-26-hive-metastore-availability"></a>

The Hive Metastore feature is available in Preview mode in the US East \(N\. Virginia\) Region\.

### Next Steps<a name="release-note-2019-11-26-hive-metastore-next-steps"></a>
+ To begin your preview, follow the instructions in the [Athena Preview Features FAQ](https://aws.amazon.com/athena/faqs/#Preview_features)\.
+ To learn more about this feature, please visit our [Using Athena Data Connector for External Hive Metastore \(Preview\)](https://docs.aws.amazon.com/athena/latest/ug/connect-to-data-source-hive.html)\.

## New Query\-Related Metrics<a name="release-note-2019-11-26-query-metrics"></a>

Athena now publishes additional query metrics that can help you understand [Amazon Athena](https://aws.amazon.com/athena/) performance\. Athena publishes query\-related metrics to [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/)\. In this release, Athena publishes the following additional query metrics:
+ **Query Planning Time** – The time taken to plan the query\. This includes the time spent retrieving table partitions from the data source\.
+ **Query Queuing Time** – The time that the query was in a queue waiting for resources\.
+ **Service Processing Time** – The time taken to write results after the query engine finishes processing\.
+ **Total Execution Time** – The time Athena took to run the query\.

To consume these new query metrics, you can create custom dashboards, set alarms and triggers on metrics in CloudWatch, or use pre\-populated dashboards directly from the Athena console\. 

### Next Steps<a name="release-note-2019-11-26-query-metrics-next-steps"></a>

For more information, see [Monitoring Athena Queries with CloudWatch Metrics](https://docs.aws.amazon.com/athena/latest/ug/query-metrics-viewing.html)\.