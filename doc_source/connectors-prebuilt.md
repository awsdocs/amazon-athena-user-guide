# Using Athena data source connectors<a name="connectors-prebuilt"></a>

This section lists prebuilt Athena data source connectors that you can use to query a variety of data sources external to Amazon S3\. To use a connector in your Athena queries, configure it and deploy it to your account\. 

## Considerations and limitations<a name="connectors-prebuilt-considerations"></a>
+  Some prebuilt connectors require that you create a VPC and a security group before you can use the connector\. For information about creating VPCs, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\. 
+  To use the Athena Federated Query feature with AWS Secrets Manager, you must configure an Amazon VPC private endpoint for Secrets Manager\. For more information, see [Create a Secrets Manager VPC private endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint-create) in the *AWS Secrets Manager User Guide*\. 
+ Queries that include a predicate take significantly longer to execute\. For small datasets, very little data is scanned, and queries take an average of about 2 minutes\. However, for large datasets, many queries can time out\.

## Additional information<a name="connectors-prebuilt-see-also"></a>
+ For information about deploying an Athena data source connector, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md)\. 
+ For information about writing queries that use Athena data source connectors, see [Writing federated queries](writing-federated-queries.md)\.
+ For in\-depth information about the Athena data source connectors, see [Available connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) on GitHub\. 

**Topics**
+ [Considerations and limitations](#connectors-prebuilt-considerations)
+ [Additional information](#connectors-prebuilt-see-also)
+ [ADLS](connectors-adls-gen2.md)
+ [Azure Synapse](connectors-azure-synapse.md)
+ [BigQuery](connectors-bigquery.md)
+ [Cloudera Hive](connectors-cloudera-hive.md)
+ [Cloudera Impala](connectors-cloudera-impala.md)
+ [CloudWatch](connectors-cloudwatch.md)
+ [CloudWatch metrics](connectors-cwmetrics.md)
+ [CMDB](connectors-cmdb.md)
+ [Db2](connectors-ibm-db2.md)
+ [DocumentDB](connectors-docdb.md)
+ [DynamoDB](connectors-dynamodb.md)
+ [HBase](connectors-hbase.md)
+ [Hortonworks](connectors-hortonworks.md)
+ [MySQL](connectors-mysql.md)
+ [Neptune](connectors-neptune.md)
+ [OpenSearch](connectors-opensearch.md)
+ [Oracle](connectors-oracle.md)
+ [PostgreSQL](connectors-postgresql.md)
+ [Redis](connectors-redis.md)
+ [Redshift](connectors-redshift.md)
+ [SAP HANA](connectors-sap-hana.md)
+ [Snowflake](connectors-snowflake.md)
+ [SQL Server](connectors-microsoft-sql-server.md)
+ [Teradata](connectors-teradata.md)
+ [Timestream](connectors-timestream.md)
+ [TPC\-DS](connectors-tpcds.md)
+ [Vertica](connectors-vertica.md)

**Note**  
The [AthenaJdbcConnector](https://serverlessrepo.aws.amazon.com/applications/us-east-1/292517598671/AthenaJdbcConnector) \(latest version 2022\.4\.1\) has been deprecated\. Instead, use a database\-specific connector like those for [MySQL](connectors-mysql.md), [Redshift](connectors-redshift.md), or [PostgreSQL](connectors-postgresql.md)\.