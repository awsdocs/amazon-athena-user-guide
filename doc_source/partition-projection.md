# Partition Projection with Amazon Athena<a name="partition-projection"></a>

You can use partition projection in Athena to speed up query processing of highly partitioned tables and automate partition management\.

In partition projection, partition values and locations are calculated from configuration rather than read from a repository like the AWS Glue Data Catalog\. Because in\-memory operations are often faster than remote operations, partition projection can reduce the runtime of queries against highly partitioned tables\. Depending on the specific characteristics of the query and underlying data, partition projection can significantly reduce query runtime for queries that are constrained on partition metadata retrieval\.

## Pruning and Projection for Heavily Partitioned Tables<a name="partition-projection-pruning-vs-projection"></a>

Partition pruning gathers metadata and "prunes" it to only the partitions that apply to your query\. This often speeds up queries\. Athena uses partition pruning for all tables with partition columns, including those tables configured for partition projection\.

Normally, when processing queries, Athena makes a `GetPartitions` call to the AWS Glue Data Catalog before performing partition pruning\. If a table has a large number of partitions, using `GetPartitions` can affect performance negatively\. To avoid this, you can use partition projection\. Partition projection allows Athena to avoid calling `GetPartitions` because the partition projection configuration gives Athena all of the necessary information to build the partitions itself\.

## Using Partition Projection<a name="partition-projection-using"></a>

To use partition projection, you specify the ranges of partition values and projection types for each partition column in the table properties in the AWS Glue Data Catalog or in your [external Hive metastore](connect-to-data-source-hive.md)\. These custom properties on the table allow Athena to know what partition patterns to expect when it runs a query on the table\. During query execution, Athena uses this information to project the partition values instead of retrieving them from the AWS Glue Data Catalog or external Hive metastore\. This not only reduces query execution time but also automates partition management because it removes the need to manually create partitions in Athena, AWS Glue, or your external Hive metastore\.

**Important**  
Enabling partition projection on a table causes Athena to ignore any partition metadata registered to the table in the AWS Glue Data Catalog or Hive metastore\.

## Use Cases<a name="partition-projection-use-cases"></a>

Scenarios in which partition projection is useful include the following:
+ Queries against a highly partitioned table do not complete as quickly as you would like\.
+ You regularly add partitions to tables as new date or time partitions are created in your data\. With partition projection, you configure relative date ranges that can be used as new data arrives\. 
+ You have highly partitioned data in Amazon S3\. The data is impractical to model in your AWS Glue Data Catalog or Hive metastore, and your queries read only small parts of it\.

### Projectable Partition Structures<a name="partition-projection-known-data-structures"></a>

Partition projection is most easily configured when your partitions follow a predictable pattern such as, but not limited to, the following:
+ **Integers** – Any continuous sequence of integers such as `[1, 2, 3, 4, ..., 1000]` or `[0500, 0550, 0600, ..., 2500]`\.
+ **Dates** – Any continuous sequence of dates or datetimes such as `[20200101, 20200102, ..., 20201231]` or `[1-1-2020 00:00:00, 1-1-2020 01:00:00, ..., 12-31-2020 23:00:00]`\.
+ **Enumerated values** – A finite set of enumerated values such as airport codes or AWS Regions\.
+ **AWS service logs** – AWS service logs typically have a known structure whose partition scheme you can specify in AWS Glue and that Athena can therefore use for partition projection\.

### Customizing the Partition Path Template<a name="partition-projection-custom-s3-storage-locations"></a>

By default, Athena builds partition locations using the form `s3://<bucket>/<table-root>/partition-col-1=<partition-col-1-val>/partition-col-2=<partition-col-2-val>/`, but if your data is organized differently, Athena offers a mechanism for customizing this path template\. For steps, see [Specifying Custom S3 Storage Locations](partition-projection-setting-up.md#partition-projection-specifying-custom-s3-storage-locations)\.

## Considerations and Limitations<a name="partition-projection-considerations-and-limitations"></a>

The following considerations apply:
+ Partition projection eliminates the need to specify partitions manually in AWS Glue or an external Hive metastore\.
+ When you enable partition projection on a table, Athena ignores any partition metadata in the AWS Glue Data Catalog or external Hive metastore for that table\.
+ If a projected partition does not exist in Amazon S3, Athena will still project the partition\. Athena does not throw an error, but no data is returned\. However, if too many of your partitions are empty, performance can be slower compared to traditional AWS Glue partitions\. If more than half of your projected partitions are empty, it is recommended that you use traditional partitions\.
+ Queries for values that are beyond the range bounds defined for partition projection do not return an error\. Instead, the query runs, but returns zero rows\. For example, if you have time\-related data that starts in 2020 and is defined as `'projection.timestamp.range'='2020/01/01,NOW'`, a query like `SELECT * FROM table-name WHERE timestamp = '2019/02/02'` will complete successfully, but return zero rows\.
+ Partition projection is usable only when the table is queried through Athena\. If the same table is read through another service such as Amazon Redshift Spectrum or Amazon EMR, the standard partition metadata is used\.
+ Because partition projection is a DML\-only feature, `SHOW PARTITIONS` does not list partitions that are projected by Athena but not registered in the AWS Glue catalog or external Hive metastore\. 
+ Athena does not use the table properties of views as configuration for partition projection\. To work around this limitation, configure and enable partition projection in the table properties for the tables that the views reference\.

## Video<a name="partition-projection-video"></a>

The following video shows how to use partition projection to improve the performance of your queries in Athena\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/iUD5pPpcyZk/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/iUD5pPpcyZk)

**Topics**
+ [Pruning and Projection for Heavily Partitioned Tables](#partition-projection-pruning-vs-projection)
+ [Using Partition Projection](#partition-projection-using)
+ [Use Cases](#partition-projection-use-cases)
+ [Considerations and Limitations](#partition-projection-considerations-and-limitations)
+ [Video](#partition-projection-video)
+ [Setting up Partition Projection](partition-projection-setting-up.md)
+ [Supported Types for Partition Projection](partition-projection-supported-types.md)
+ [Dynamic ID Partitioning](partition-projection-dynamic-id-partitioning.md)
+ [Amazon Kinesis Data Firehose Example](partition-projection-kinesis-firehose-example.md)