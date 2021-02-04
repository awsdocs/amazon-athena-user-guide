# Using Athena to Query Apache Hudi Datasets<a name="querying-hudi"></a>

[https://hudi.incubator.apache.org/](https://hudi.incubator.apache.org/) is an open\-source data management framework that simplifies incremental data processing\. Record\-level insert, update, upsert, and delete actions are processed much more granularly, reducing overhead\. `Upsert` refers to the ability to insert records into an existing dataset if they do not already exist or to update them if they do\.

Hudi handles data insertion and update events without creating many small files that can cause performance issues for analytics\. Apache Hudi automatically tracks changes and merges files so that they remain optimally sized\. This avoids the need to build custom solutions that monitor and re\-write many small files into fewer large files\.

Hudi datasets are suitable for the following use cases:
+ Complying with privacy regulations like [General Data Protection Regulation](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) \(GDPR\) and [California Consumer Privacy Act](https://en.wikipedia.org/wiki/California_Consumer_Privacy_Act) \(CCPA\) that enforce people's right to remove personal information or change how their data is used\.
+ Working with streaming data from sensors and other Internet of Things \(IoT\) devices that require specific data insertion and update events\.
+ Implementing a [change data capture \(CDC\) system](https://en.wikipedia.org/wiki/Change_data_capture)

Data sets managed by Hudi are stored in S3 using open storage formats\. Currently, Athena can read compacted Hudi datasets but not write Hudi data\. Athena uses Apache Hudi version 0\.5\.2\-incubating, subject to change\. For more information about this Hudi version, see [apache/hudi release\-0\.5\.2](https://github.com/apache/hudi/tree/release-0.5.2) on GitHub\.com\.

## Hudi Dataset Storage Types<a name="querying-hudi-hudi-dataset-storage-types"></a>

A Hudi dataset can be one of the following types:
+ **Copy on Write \(CoW\)** – Data is stored in a columnar format \(Parquet\), and each update creates a new version of files during a write\.
+ **Merge on Read \(MoR\)** – Data is stored using a combination of columnar \(Parquet\) and row\-based \(Avro\) formats\. Updates are logged to row\-based `delta` files and are compacted as needed to create new versions of the columnar files\.

With CoW datasets, each time there is an update to a record, the file that contains the record is rewritten with the updated values\. With a MoR dataset, each time there is an update, Hudi writes only the row for the changed record\. MoR is better suited for write\- or change\-heavy workloads with fewer reads\. CoW is better suited for read\-heavy workloads on data that change less frequently\.

Hudi provides three logical views for accessing the data:
+ **Read\-optimized view** – Provides the latest committed dataset from CoW tables and the latest compacted dataset from MoR tables\.
+ **Incremental view** – Provides a change stream between two actions out of a CoW dataset to feed downstream jobs and extract, transform, load \(ETL\) workflows\.
+ **Real\-time view** – Provides the latest committed data from a MoR table by merging the columnar and row\-based files inline\.

Currently, Athena supports only the first of these: the read\-optimized view\. Queries on a read\-optimized view return all compacted data, which provides good performance but does not include the latest delta commits\. For more information about the tradeoffs between storage types, see [Storage Types & Views](https://hudi.apache.org/docs/0.5.0-concepts.html#storage-types--views) in the Apache Hudi documentation\.

## Considerations and Limitations<a name="querying-hudi-in-athena-considerations-and-limitations"></a>
+ Athena supports reading of the compacted view of Hudi data only\.
  + For Copy on Write \(CoW\), Athena supports snapshot queries\.
  + For Merge on Read \(MoR\), Athena supports read optimized queries\.
+ Athena does not support [CTAS](ctas.md) or [INSERT INTO](insert-into.md) on Hudi data\. If you would like Athena support for writing Hudi datasets, send feedback to athena\-feedback@amazon\.com\.

  For more information about writing Hudi data, see the following resources:
  + [Working With a Hudi Dataset](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hudi-work-with-dataset.html) in the [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/)\.
  + [Writing Hudi Tables](https://hudi.apache.org/docs/0.5.1-writing_data.html) in the Apache Hudi documentation\.
+ Using MSCK REPAIR TABLE on Hudi tables in Athena is not supported\. If you need to load a Hudi table not created in AWS Glue, use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\.

## Video<a name="querying-hudi-videos"></a>

The following video shows how you can use Amazon Athena to query the read\-optimized view of an Apache Hudi dataset in your Amazon S3\-based data lake\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/TVcreqxBaGA/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/TVcreqxBaGA)

## Creating Hudi Tables<a name="querying-hudi-in-athena-creating-hudi-tables"></a>

This section provides examples of CREATE TABLE statements in Athena for partitioned and nonpartitioned tables of Hudi data\.

If you have Hudi tables already created in AWS Glue, you can query them directly in Athena\. When you create Hudi tables in Athena, you must run ALTER TABLE ADD PARTITION to load the Hudi data before you can query it\.

### Copy on Write \(CoW\) Create Table Examples<a name="querying-hudi-copy-on-write-create-table-examples"></a>

#### Nonpartitioned CoW Table<a name="querying-hudi-nonpartitioned-cow-table"></a>

The following example creates a nonpartitioned CoW table in Athena\.

```
CREATE EXTERNAL TABLE `non_partition_cow`(
  `_hoodie_commit_time` string,
  `_hoodie_commit_seqno` string,
  `_hoodie_record_key` string,
  `_hoodie_partition_path` string,
  `_hoodie_file_name` string,
  `event_id` string,
  `event_time` string,
  `event_name` string,
  `event_guests` int,
  `event_type` string)
ROW FORMAT SERDE
  'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
STORED AS INPUTFORMAT
  'org.apache.hudi.hadoop.HoodieParquetInputFormat'
OUTPUTFORMAT
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://bucket/folder/non_partition_cow'
```

#### Partitioned CoW Table<a name="querying-hudi-partitioned-cow-table"></a>

The following example creates a partitioned CoW table in Athena\.

```
CREATE EXTERNAL TABLE `partition_cow`(
  `_hoodie_commit_time` string, 
  `_hoodie_commit_seqno` string, 
  `_hoodie_record_key` string, 
  `_hoodie_partition_path` string, 
  `_hoodie_file_name` string, 
  `event_id` string, 
  `event_time` string, 
  `event_name` string, 
  `event_guests` int)
PARTITIONED BY ( 
  `event_type` string)
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hudi.hadoop.HoodieParquetInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://bucket/folder/partition_cow'
```

The following `ALTER TABLE ADD PARTITION` example adds two partitions to the example `partition_cow` table\.

```
ALTER TABLE partition_cow ADD
  PARTITION (event_type = 'one') LOCATION 's3://bucket/folder/partition_cow/one/'
  PARTITION (event_type = 'two') LOCATION 's3://bucket/folder/partition_cow/two/'
```

### Merge on Read \(MoR\) Create Table Examples<a name="querying-hudi-merge-on-read-create-table-examples"></a>

Hudi creates two tables in the Hive metastore for MoR: a table with the name that you specified, which is a read\-optimized view, and a table with the same name appended with `_rt`, which is a real\-time view\. However, when you create MoR tables in Athena, you can query only the read\-optimized view\.

#### Nonpartitioned Merge on Read \(MoR\) Table<a name="querying-hudi-nonpartitioned-merge-on-read-table"></a>

The following example creates a nonpartitioned MoR table in Athena\.

```
CREATE EXTERNAL TABLE `nonpartition_mor`(
  `_hoodie_commit_time` string, 
  `_hoodie_commit_seqno` string, 
  `_hoodie_record_key` string, 
  `_hoodie_partition_path` string, 
  `_hoodie_file_name` string, 
  `event_id` string, 
  `event_time` string, 
  `event_name` string, 
  `event_guests` int, 
  `event_type` string)
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hudi.hadoop.HoodieParquetInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://bucket/folder/nonpartition_mor'
```

#### Partitioned Merge on Read \(MoR\) Table<a name="querying-hudi-partitioned-merge-on-read-table"></a>

The following example creates a partitioned MoR table in Athena\.

```
CREATE EXTERNAL TABLE `partition_mor`(
  `_hoodie_commit_time` string, 
  `_hoodie_commit_seqno` string, 
  `_hoodie_record_key` string, 
  `_hoodie_partition_path` string, 
  `_hoodie_file_name` string, 
  `event_id` string, 
  `event_time` string, 
  `event_name` string, 
  `event_guests` int)
PARTITIONED BY ( 
  `event_type` string)
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hudi.hadoop.HoodieParquetInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://bucket/folder/partition_mor'
```

The following `ALTER TABLE ADD PARTITION` example adds two partitions to the example `partition_mor` table\.

```
ALTER TABLE partition_mor ADD
  PARTITION (event_type = 'one') LOCATION 's3://bucket/folder/partition_mor/one/'
  PARTITION (event_type = 'two') LOCATION 's3://bucket/folder/partition_mor/two/'
```

## See Also<a name="querying-hudi-see-also"></a>

For information on using AWS Glue custom connectors and AWS Glue 2\.0 jobs to create an Apache Hudi table that you can query with Athena, see [Writing to Apache Hudi tables using AWS Glue Custom Connector](http://aws.amazon.com/blogs/big-data/writing-to-apache-hudi-tables-using-aws-glue-connector/) in the AWS Big Data Blog\.