# Using Athena Data Source Connectors<a name="athena-prebuilt-data-connectors"></a>

This section lists prebuilt Athena data source connectors that you can use to query a variety of data sources external to Amazon S3\. To use a connector in your Athena queries, configure it and deploy it to your account\. 

**Notes**  
Before you start, note the following points:
+ To use the Amazon Athena Federated Query feature, set your workgroup to Athena engine version 2\. For steps, see [Changing Athena Engine Versions](engine-versions-changing.md)\.
+  To use the Athena Federated Query feature with AWS Secrets Manager, you must configure an Amazon VPC private endpoint for Secrets Manager\. For more information, see [Create a Secrets Manager VPC Private Endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint-create) in the *AWS Secrets Manager User Guide*\. 

For more information about data source connectors, see the following topics:
+ For information about deploying an Athena data source connector, see [Deploying a Connector and Connecting to a Data Source](connect-to-a-data-source-lambda.md)\. 
+ For information about writing queries that use Athena data source connectors, see [Writing Federated Queries](writing-federated-queries.md)\.
+ For in\-depth information about the Athena data source connectors, see [Available Connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) on GitHub\. 

**Topics**
+ [Amazon Athena CloudWatch Connector](athena-prebuilt-data-connectors-cwlogs.md)
+ [Amazon Athena CloudWatch Metrics Connector](athena-prebuilt-data-connectors-cwmetrics.md)
+ [Athena AWS CMDB Connector](athena-prebuilt-data-connectors-cmdb.md)
+ [Amazon Athena DocumentDB Connector](athena-prebuilt-data-connectors-docdb.md)
+ [Amazon Athena DynamoDB Connector](athena-prebuilt-data-connectors-dynamodb.md)
+ [Amazon Athena OpenSearch Connector](athena-prebuilt-data-connectors-elasticsearch.md)
+ [Amazon Athena HBase Connector](athena-prebuilt-data-connectors-hbase.md)
+ [Amazon Athena Connector for JDBC\-Compliant Data Sources \(PostgreSQL, MySQL, and Amazon Redshift\)](athena-prebuilt-data-connectors-jdbc.md)
+ [Amazon Athena Neptune Connector](athena-prebuilt-data-connectors-neptune.md)
+ [Amazon Athena Redis Connector](athena-prebuilt-data-connectors-redis.md)
+ [Amazon Athena Timestream Connector](athena-prebuilt-data-connectors-timestream.md)
+ [Amazon Athena TPC Benchmark DS \(TPC\-DS\) Connector](athena-prebuilt-data-connectors-tpcds.md)
+ [Amazon Athena Vertica Connector](athena-prebuilt-data-connectors-vertica.md)