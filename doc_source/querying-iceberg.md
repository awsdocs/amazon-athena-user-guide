# Using Iceberg Tables<a name="querying-iceberg"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

In this preview release, Athena supports read, time travel, and write queries for Apache Iceberg tables that use the Apache Parquet format for data and the AWS Glue catalog for their metastore\.

[Apache Iceberg](https://iceberg.apache.org/) is an open table format for very large analytic datasets\. Iceberg manages large collections of files as tables, and it supports modern analytical data lake operations such as record\-level insert, update, delete, and time travel queries\. The Iceberg specification allows seamless table evolution such as schema and partition evolution, and its design is optimized for usage on Amazon S3\. Iceberg also helps guarantee data correctness under concurrent write scenarios\.

For more information about Apache Iceberg, see [https://iceberg\.apache\.org/](https://iceberg.apache.org/)\.

## Considerations and Limitations<a name="querying-iceberg-considerations-and-limitations"></a>

In this preview release, Athena support for Iceberg tables has the following limitations:
+ **Regional availability** – This preview is available in the AWS Regions US East \(N\. Virginia\), US West \(Oregon\), and Europe \(Ireland\)\. 
+ **Tables with AWS Glue catalog only** – Only Iceberg tables created against the AWS Glue catalog based on specifications defined by the [open source Glue Catalog implementation](https://iceberg.apache.org/#aws/#glue-catalog) are supported from Athena\.
+ **Parquet files only** – Currently, Athena supports Iceberg tables in Parquet file format only\. ORC and AVRO are not supported\.
+ **Iceberg v2 tables only** – Athena only creates and operates on Iceberg v2 tables\. For the difference between v1 and v2 tables, see [Format version changes](https://iceberg.apache.org/#spec/#appendix-e-format-version-changes) in the Apache Iceberg documentation\.
+ **AmazonAthenaIcebergPreview workgroup** – Query operations must be performed from a workgroup that you create in Athena called `AmazonAthenaIcebergPreview`\. Queries on Iceberg tables performed outside this workgroup may have impaired functionality, or produce unexpected behavior\. For information about creating workgroups, see [Create a Workgroup](workgroups-create-update-delete.md#creating-workgroups) and [Setting up Workgroups](workgroups-procedure.md)\.
+ **Table locking support by AWS Glue only** – Transactional writes on Iceberg tables in Athena are implemented by AWS Glue\. This is different from open source [DynamoDB\-based table locking](https://iceberg.apache.org/#aws/#dynamodb-for-commit-locking)\. If more than one locking mechanism is used, concurrent writes may not be detectable, and data correctness issues can occur\.
+ **Display of time types without time zone** – The time and timestamp without time zone types are displayed in UTC\. If the time zone is unspecified in a filter expression on a time column, UTC is used\.
+ **Limited support for tables with type promotion** – During schema evolution, Iceberg allows column type promotion for certain types\. However, Athena cannot query tables with a column that underwent a `float` to `double` type promotion unless the table had no data when the type was `float`\.
+  **Timestamp related data precision** – While Iceberg supports microsecond precision for the timestamp data type, Athena supports only millisecond precision for timestamps in both reads and writes\. Athena only retains millisecond precision in time related columns for data that is rewritten during manual compaction operations\.

## Getting Started<a name="querying-iceberg-getting-started"></a>

To get started querying Iceberg tables using Athena, perform the following steps\.

1. **Create the AmazonAthenaIcebergPreview workgroup**\. In Athena, create a workgroup called `AmazonAthenaIcebergPreview`\. You must use this workgroup to run queries against the Iceberg tables that you create\.

1. **Create an Iceberg table that uses an AWS Glue catalog**\. To create an Iceberg managed table from Athena, use a `CREATE TABLE` statement that specifies the table property `'table_type' ='ICEBERG'` as described in [Creating Iceberg Tables](querying-iceberg-creating-tables.md)\.