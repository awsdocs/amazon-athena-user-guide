# Using Athena SQL<a name="using-athena-sql"></a>

You can use Athena SQL to query your data in\-place in Amazon S3 using the [AWS Glue Data Catalog](glue-athena.md), [an external Hive metastore](connect-to-data-source-hive.md), or [federated queries](connect-to-a-data-source.md) using a variety of [prebuilt connectors](connectors-prebuilt.md) to other data sources\.

You can also:
+ Connect to business intelligence tools and other applications using [Athena's JDBC and ODBC drivers](https://docs.aws.amazon.com/athena/latest/ug/athena-bi-tools-jdbc-odbc.html)\. 
+ Query [AWS service logs](querying-aws-service-logs.md)\. 
+ Query [Apache Iceberg tables](querying-iceberg.md), including time travel queries, and [Apache Hudi datasets](querying-hudi.md)\. 
+ Query [geospatial data](querying-geospatial-data.md)\. 
+ Query using [machine learning inference](https://docs.aws.amazon.com/athena/latest/ug/querying-mlmodel.html) from Amazon SageMaker\.
+ Query using your own [user\-defined functions](https://docs.aws.amazon.com/athena/latest/ug/querying-udf.html)\.
+ Speed up query processing of highly\-partitioned tables and automate partition management by using [partition projection](https://docs.aws.amazon.com/athena/latest/ug/partition-projection.html)\.

**Topics**
+ [Understanding tables, databases, and the Data Catalog](understanding-tables-databases-and-the-data-catalog.md)
+ [Getting started](getting-started.md)
+ [Connecting to data sources](work-with-data-stores.md)
+ [Creating databases and tables](work-with-data.md)
+ [Creating a table from query results \(CTAS\)](ctas.md)
+ [Athena compression support](compression-formats.md)
+ [SerDe reference](serde-reference.md)
+ [Running SQL queries using Amazon Athena](querying-athena-tables.md)
+ [Using Athena ACID transactions](acid-transactions.md)
+ [Amazon Athena security](security.md)
+ [Using workgroups to control query access and costs](manage-queries-control-costs-with-workgroups.md)
+ [Tagging Athena resources](tags.md)
+ [Athena engine versioning](engine-versions.md)
+ [SQL reference for Athena](ddl-sql-reference.md)
+ [Troubleshooting in Athena](troubleshooting-athena.md)
+ [Performance tuning in Athena](performance-tuning.md)
+ [Service Quotas](service-limits.md)
+ [Code samples](code-samples.md)