# Using Athena data source connectors<a name="athena-prebuilt-data-connectors"></a>

This section lists prebuilt Athena data source connectors that you can use to query a variety of data sources external to Amazon S3\. To use a connector in your Athena queries, configure it and deploy it to your account\. 

**Notes**  
Before you start, note the following points:
+ To use the Amazon Athena Federated Query feature, set your workgroup to Athena engine version 2\. For steps, see [Changing Athena engine versions](engine-versions-changing.md)\.
+  Some prebuilt connectors require that you create a VPC and a security group before you can use the connector\. For information about creating VPCs, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\. 
+  To use the Athena Federated Query feature with AWS Secrets Manager, you must configure an Amazon VPC private endpoint for Secrets Manager\. For more information, see [Create a Secrets Manager VPC private endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint-create) in the *AWS Secrets Manager User Guide*\. 

For more information about data source connectors, see the following topics:
+ For information about deploying an Athena data source connector, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md)\. 
+ For information about writing queries that use Athena data source connectors, see [Writing federated queries](writing-federated-queries.md)\.
+ For in\-depth information about the Athena data source connectors, see [Available connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) on GitHub\. 

**Topics**
+ [Amazon Athena Azure Data Lake Storage \(ADLS\) Gen2 connector](athena-prebuilt-data-connectors-adls-gen2.md)
+ [Amazon Athena Azure Synapse connector](athena-prebuilt-data-connectors-azure-synapse.md)
+ [Amazon Athena Google BigQuery connector](athena-prebuilt-data-connectors-google-bigquery.md)
+ [Amazon Athena Cloudera Hive connector](athena-prebuilt-data-connectors-cloudera-hive.md)
+ [Amazon Athena Cloudera Impala connector](athena-prebuilt-data-connectors-cloudera-impala.md)
+ [Amazon Athena CloudWatch connector](athena-prebuilt-data-connectors-cwlogs.md)
+ [Amazon Athena CloudWatch metrics connector](athena-prebuilt-data-connectors-cwmetrics.md)
+ [Amazon Athena AWS CMDB connector](athena-prebuilt-data-connectors-cmdb.md)
+ [Amazon Athena DocumentDB connector](athena-prebuilt-data-connectors-docdb.md)
+ [Amazon Athena DynamoDB connector](athena-prebuilt-data-connectors-dynamodb.md)
+ [Amazon Athena HBase connector](athena-prebuilt-data-connectors-hbase.md)
+ [Amazon Athena Hortonworks connector](athena-prebuilt-data-connectors-hortonworks.md)
+ [Amazon Athena MySQL connector](athena-prebuilt-data-connectors-mysql.md)
+ [Amazon Athena Neptune connector](athena-prebuilt-data-connectors-neptune.md)
+ [Amazon Athena OpenSearch connector](athena-prebuilt-data-connectors-elasticsearch.md)
+ [Amazon Athena Oracle connector](athena-prebuilt-data-connectors-oracle.md)
+ [Amazon Athena PostgreSQL connector](athena-prebuilt-data-connectors-postgresql.md)
+ [Amazon Athena Redis connector](athena-prebuilt-data-connectors-redis.md)
+ [Amazon Athena Redshift connector](athena-prebuilt-data-connectors-redshift.md)
+ [Amazon Athena SAP HANA connector](athena-prebuilt-data-connectors-sap-hana.md)
+ [Amazon Athena Snowflake connector](athena-prebuilt-data-connectors-snowflake.md)
+ [Amazon Athena Microsoft SQL Server connector](athena-prebuilt-data-connectors-microsoft-sql-server.md)
+ [Amazon Athena Teradata connector](athena-prebuilt-data-connectors-teradata.md)
+ [Amazon Athena Timestream connector](athena-prebuilt-data-connectors-timestream.md)
+ [Amazon Athena TPC benchmark DS \(TPC\-DS\) connector](athena-prebuilt-data-connectors-tpcds.md)
+ [Amazon Athena Vertica connector](athena-prebuilt-data-connectors-vertica.md)