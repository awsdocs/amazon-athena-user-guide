# Connecting to data sources<a name="work-with-data-stores"></a>

You can use Amazon Athena to query data stored in different locations and formats in a *dataset*\. This dataset might be in CSV, JSON, Avro, Parquet, or some other format\.

The tables and databases that you work with in Athena to run queries are based on *metadata*\. Metadata is data about the underlying data in your dataset\. How that metadata describes your dataset is called the *schema*\. For example, a table name, the column names in the table, and the data type of each column are schema, saved as metadata, that describe an underlying dataset\. In Athena, we call a system for organizing metadata a *data catalog* or a *metastore*\. The combination of a dataset and the data catalog that describes it is called a *data source*\.

The relationship of metadata to an underlying dataset depends on the type of data source that you work with\. Relational data sources like MySQL, PostgreSQL, and SQL Server tightly integrate the metadata with the dataset\. In these systems, the metadata is most often written when the data is written\. Other data sources, like those built using [Hive](https://hive.apache.org), allow you to define metadata on\-the\-fly when you read the dataset\. The dataset can be in a variety of formats—for example, CSV, JSON, Parquet, or Avro\.

Athena natively supports the AWS Glue Data Catalog\. The AWS Glue Data Catalog is a data catalog built on top of other datasets and data sources such as Amazon S3, Amazon Redshift, and Amazon DynamoDB\. You can also connect Athena to other data sources by using a variety of connectors\.

**Topics**
+ [Integration with AWS Glue](glue-athena.md)
+ [Using Athena Data Connector for External Hive Metastore](connect-to-data-source-hive.md)
+ [Using Amazon Athena Federated Query](connect-to-a-data-source.md)
+ [IAM policies for accessing data catalogs](datacatalogs-iam-policy.md)
+ [Managing data sources](data-sources-managing.md)
+ [Connecting to Amazon Athena with ODBC and JDBC drivers](athena-bi-tools-jdbc-odbc.md)