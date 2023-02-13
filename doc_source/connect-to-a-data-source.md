# Using Amazon Athena Federated Query<a name="connect-to-a-data-source"></a>

If you have data in sources other than Amazon S3, you can use Athena Federated Query to query the data in place or build pipelines that extract data from multiple data sources and store them in Amazon S3\. With Athena Federated Query, you can run SQL queries across data stored in relational, non\-relational, object, and custom data sources\.

Athena uses *data source connectors* that run on AWS Lambda to run federated queries\. A data source connector is a piece of code that can translate between your target data source and Athena\. You can think of a connector as an extension of Athena's query engine\. Prebuilt Athena data source connectors exist for data sources like Amazon CloudWatch Logs, Amazon DynamoDB, Amazon DocumentDB, and Amazon RDS, and JDBC\-compliant relational data sources such MySQL, and PostgreSQL under the Apache 2\.0 license\. You can also use the Athena Query Federation SDK to write custom connectors\. To choose, configure, and deploy a data source connector to your account, you can use the Athena and Lambda consoles or the AWS Serverless Application Repository\. After you deploy data source connectors, the connector is associated with a catalog that you can specify in SQL queries\. You can combine SQL statements from multiple catalogs and span multiple data sources with a single query\.

When a query is submitted against a data source, Athena invokes the corresponding connector to identify parts of the tables that need to be read, manages parallelism, and pushes down filter predicates\. Based on the user submitting the query, connectors can provide or restrict access to specific data elements\. Connectors use Apache Arrow as the format for returning data requested in a query, which enables connectors to be implemented in languages such as C, C\+\+, Java, Python, and Rust\. Since connectors are processed in Lambda, they can be used to access data from any data source on the cloud or on\-premises that is accessible from Lambda\.

To write your own data source connector, you can use the Athena Query Federation SDK to customize one of the prebuilt connectors that Amazon Athena provides and maintains\. You can modify a copy of the source code from the [GitHub repository](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) and then use the [Connector publish tool](https://github.com/awslabs/aws-athena-query-federation/wiki/Connector_Publish_Tool) to create your own AWS Serverless Application Repository package\. 

**Note**  
Third party developers may have used the Athena Query Federation SDK to write data source connectors\. For support or licensing issues with these data source connectors, please work with your connector provider\. These connectors are not tested or supported by AWS\. 

For a list of data source connectors written and tested by Athena, see [Using Athena data source connectors](connectors-prebuilt.md)\.

For information about writing your own data source connector, see [Example Athena connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.

## Considerations and limitations<a name="connect-to-a-data-source-considerations"></a>
+ **Engine versions** – Athena Federated Query is supported only on Athena engine version 2 and later versions\. For information about Athena engine versions, see [Athena engine versioning](engine-versions.md)\.
+ **Views** – You cannot use views with federated data sources\.
+ **Write operations** – Write operations like [INSERT INTO](insert-into.md) are not supported\. Attempting to do so may result in the error message This operation is currently not supported for external catalogs\.
+  **Pricing** – For pricing information, see [Amazon Athena pricing](http://aws.amazon.com/athena/pricing/)\.

  **JDBC driver** – To use the JDBC driver with federated queries or an [external Hive metastore](connect-to-data-source-hive.md), include `MetadataRetrievalMethod=ProxyAPI` in your JDBC connection string\. For information about the JDBC driver, see [Connecting to Amazon Athena with JDBC](connect-with-jdbc.md)\. 
+ **Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, you must configure an Amazon VPC private endpoint for Secrets Manager\. For more information, see [Create a Secrets Manager VPC private endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint-create) in the *AWS Secrets Manager User Guide*\.

Data source connectors might require access to the following resources to function correctly\. If you use a prebuilt connector, check the information for the connector to ensure that you have configured your VPC correctly\. Also, ensure that IAM principals running queries and creating connectors have privileges to required actions\. For more information, see [Example IAM permissions policies to allow Athena Federated Query](federated-query-iam-access.md)\.
+ **Amazon S3** – In addition to writing query results to the Athena query results location in Amazon S3, data connectors also write to a spill bucket in Amazon S3\. Connectivity and permissions to this Amazon S3 location are required\.
+ **Athena** – Data sources need connectivity to Athena and vice versa for checking query status and preventing overscan\.
+ **AWS Glue Data Catalog** – Connectivity and permissions are required if your connector uses Data Catalog for supplemental or primary metadata\.

For the most up\-to\-date information about known issues and limitations, see [Limitations and issues](https://github.com/awslabs/aws-athena-query-federation/wiki/Limitations_And_Issues) in the aws\-athena\-query\-federation GitHub repository\.

## Videos<a name="connect-to-a-data-source-videos"></a>

Watch the following videos to learn more about using Athena Federated Query\.

**Video: Analyze Results of Federated Query in Amazon Athena in Amazon QuickSight**  
The following video demonstrates how to analyze results of an Athena federated query in Amazon QuickSight\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/HyM5d0TmwAQ/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/HyM5d0TmwAQ)

**Video: Game Analytics Pipeline**  
The following video shows how to deploy a scalable serverless data pipeline to ingest, store, and analyze telemetry data from games and services using Amazon Athena federated queries\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/xcS-flUMVbs/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/xcS-flUMVbs)