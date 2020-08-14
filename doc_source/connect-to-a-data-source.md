# Using Amazon Athena Federated Query \(Preview\)<a name="connect-to-a-data-source"></a>

If you have data in sources other than Amazon S3, you can use Athena Federated Query \(Preview\) to query the data in place or build pipelines that extract data from multiple data sources and store them in Amazon S3\. With Athena Federated Query \(Preview\), you can run SQL queries across data stored in relational, non\-relational, object, and custom data sources\.

Athena uses *data source connectors* that run on AWS Lambda to run federated queries\. A data source connector is a piece of code that can translate between your target data source and Athena\. You can think of a connector as an extension of Athena's query engine\. Prebuilt Athena data source connectors exist for data sources like Amazon CloudWatch Logs, Amazon DynamoDB, Amazon DocumentDB, and Amazon RDS, and JDBC\-compliant relational data sources such MySQL, and PostgreSQL under the Apache 2\.0 license\. You can also use the Athena Query Federation SDK to write custom connectors\. To choose, configure, and deploy a data source connector to your account, you can use the Athena and Lambda consoles or the AWS Serverless Application Repository\. After you deploy data source connectors, the connector is associated with a catalog that you can specify in SQL queries\. You can combine SQL statements from multiple catalogs and span multiple data sources with a single query\.

When a query is submitted against a data source, Athena invokes the corresponding connector to identify parts of the tables that need to be read, manages parallelism, and pushes down filter predicates\. Based on the user submitting the query, connectors can provide or restrict access to specific data elements\. Connectors use Apache Arrow as the format for returning data requested in a query, which enables connectors to be implemented in languages such as C, C\+\+, Java, Python, and Rust\. Since connectors are processed in Lambda, they can be used to access data from any data source on the cloud or on\-premises that is accessible from Lambda\.

To write your own data source connector, you can use the Athena Query Federation SDK to customize one of the prebuilt connectors that Amazon Athena provides and maintains\. You can modify a copy of the source code from the [GitHub repository](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) and then use the [Connector Publish Tool](https://github.com/awslabs/aws-athena-query-federation/wiki/Connector_Publish_Tool) to create your own AWS Serverless Application Repository package\. 

For a list of available Athena data source connectors, see [Using Athena Data Source Connectors](athena-prebuilt-data-connectors.md)\.

For information about writing your own data source connector, see [Example Athena Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.

## Considerations and Limitations<a name="connect-to-a-data-source-considerations"></a>
+ **Available Regions** – The Athena federated query feature is available in preview in the US East \(N\. Virginia\), Asia Pacific \(Mumbai\), Europe \(Ireland\), and US West \(Oregon\) Regions\. 
+ **AmazonAthenaPreviewFunctionality workgroup** – To use this feature in preview, you must create an Athena workgroup named `AmazonAthenaPreviewFunctionality` and join that workgroup\. For more information, see [Managing Workgroups](workgroups-create-update-delete.md)\. 
+ **Views** – You cannot use views with federated data sources\.

Data source connectors might require access to the following resources to function correctly\. If you use a prebuilt connector, check the information for the connector to ensure that you have configured your VPC correctly\. Also, ensure that IAM principals running queries and creating connectors have privileges to required actions\. For more information, see [Example IAM Permissions Policies to Allow Athena Federated Query \(Preview\)](federated-query-iam-access.md)\.
+ **Amazon S3** – In addition to writing query results to the Athena query results location in Amazon S3, data connectors also write to a spill bucket in Amazon S3\. Connectivity and permissions to this Amazon S3 location are required\.
+ **Athena** – Data sources need connectivity to Athena and vice versa for checking query status and preventing overscan\.
+ **AWS Glue Data Catalog** – Connectivity and permissions are required if your connector uses Data Catalog for supplemental or primary metadata\.

For the most up\-to\-date information about known issues and limitations, see [Limitations and Issues](https://github.com/awslabs/aws-athena-query-federation/wiki/Limitations_And_Issues) in the aws\-athena\-query\-federation GitHub repository\.