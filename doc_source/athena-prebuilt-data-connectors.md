# Using Athena Data Source Connectors<a name="athena-prebuilt-data-connectors"></a>

This section lists prebuilt Athena data source connectors that you can use to query a variety of data sources external to Amazon S3\. To use a connector in your Athena queries, configure it and deploy it to your account\. 

**Note**  
To use this feature in preview, you must create an Athena workgroup named `AmazonAthenaPreviewFunctionality` and join that workgroup\. For more information, see [Managing Workgroups](workgroups-create-update-delete.md)\. 

See the following topics for more information:
+ For information about deploying an Athena data source connector, see [Deploying a Connector and Connecting to a Data Source](connect-to-a-data-source-lambda.md)\. 
+ For information about writing queries that use Athena data source connectors, see [Writing Federated Queries](writing-federated-queries.md)\.
+ For complete information about the Athena data source connectors, see [Available Connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) on GitHub\. 

**Topics**
+ [Athena AWS CMDB Connector](athena-prebuilt-data-connectors-cmdb.md)
+ [Amazon Athena CloudWatch Connector](athena-prebuilt-data-connectors-cwlogs.md)
+ [Amazon Athena CloudWatch Metrics Connector](athena-prebuilt-data-connectors-cwmetrics.md)
+ [Amazon Athena DocumentDB Connector](athena-prebuilt-data-connectors-docdb.md)
+ [Amazon Athena DynamoDB Connector](athena-prebuilt-data-connectors-dynamodb.md)
+ [Amazon Athena Elasticsearch Connector](athena-prebuilt-data-connectors-elasticsearch.md)
+ [Amazon Athena HBase Connector](athena-prebuilt-data-connectors-hbase.md)
+ [Amazon Athena Connector for JDBC\-Compliant Data Sources](athena-prebuilt-data-connectors-jdbc.md)
+ [Amazon Athena Redis Connector](athena-prebuilt-data-connectors-redis.md)
+ [Amazon Athena TPC Benchmark DS \(TPC\-DS\) Connector](athena-prebuilt-data-connectors-tpcds.md)