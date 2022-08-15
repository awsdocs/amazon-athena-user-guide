# Using Athena to query Apache Hudi datasets<a name="querying-hudi"></a>

[https://hudi.incubator.apache.org/](https://hudi.incubator.apache.org/) is an open\-source data management framework that simplifies incremental data processing\. Record\-level insert, update, upsert, and delete actions are processed much more granularly, reducing overhead\. `Upsert` refers to the ability to insert records into an existing dataset if they do not already exist or to update them if they do\.

Hudi handles data insertion and update events without creating many small files that can cause performance issues for analytics\. Apache Hudi automatically tracks changes and merges files so that they remain optimally sized\. This avoids the need to build custom solutions that monitor and re\-write many small files into fewer large files\.

Hudi datasets are suitable for the following use cases:
+ Complying with privacy regulations like [General data protection regulation](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) \(GDPR\) and [California consumer privacy act](https://en.wikipedia.org/wiki/California_Consumer_Privacy_Act) \(CCPA\) that enforce people's right to remove personal information or change how their data is used\.
+ Working with streaming data from sensors and other Internet of Things \(IoT\) devices that require specific data insertion and update events\.
+ Implementing a [change data capture \(CDC\) system](https://en.wikipedia.org/wiki/Change_data_capture)\.

Data sets managed by Hudi are stored in S3 using open storage formats\. Currently, Athena can read compacted Hudi datasets but not write Hudi data\. Athena uses Apache Hudi version 0\.8\.0, subject to change\. For more information about this Hudi version, see [Release 0\.8\.0 \(docs\)](https://hudi.apache.org/releases/release-0.8.0) on the Apache website\.

## Hudi dataset table types<a name="querying-hudi-hudi-dataset-table-types"></a>

A Hudi dataset can be one of the following types:
+ **Copy on Write \(CoW\)** – Data is stored in a columnar format \(Parquet\), and each update creates a new version of files during a write\.
+ **Merge on Read \(MoR\)** – Data is stored using a combination of columnar \(Parquet\) and row\-based \(Avro\) formats\. Updates are logged to row\-based `delta` files and are compacted as needed to create new versions of the columnar files\.

With CoW datasets, each time there is an update to a record, the file that contains the record is rewritten with the updated values\. With a MoR dataset, each time there is an update, Hudi writes only the row for the changed record\. MoR is better suited for write\- or change\-heavy workloads with fewer reads\. CoW is better suited for read\-heavy workloads on data that change less frequently\.

Hudi provides three query types for accessing the data:
+ **Snapshot queries** – Queries that see the latest snapshot of the table as of a given commit or compaction action\. For MoR tables, snapshot queries expose the most recent state of the table by merging the base and delta files of the latest file slice at the time of the query\. 
+ **Incremental queries** – Queries only see new data written to the table, since a given commit/compaction\. This effectively provides change streams to enable incremental data pipelines\.
+ **Read optimized queries** – For MoR tables, queries see the latest data compacted\. For CoW tables, queries see the latest data committed\.

The following table shows the possible Hudi query types for each table type\.


| Table type | Possible Hudi query types | 
| --- | --- | 
| Copy On Write | snapshot, incremental | 
| Merge On Read | snapshot, incremental, read optimized | 

Currently, Athena supports snapshot queries and read optimized queries, but not incremental queries\. On MoR tables, all data exposed to read optimized queries are compacted\. This provides good performance but does not include the latest delta commits\. Snapshot queries contain the freshest data but incur some computational overhead, which makes these queries less performant\. 

For more information about the tradeoffs between table and query types, see [Table & Query Types](https://hudi.apache.org/docs/table_types/) in the Apache Hudi documentation\.

### Hudi terminology change: Views are now queries<a name="querying-hudi-hudi-dataset-table-types-terminology"></a>

Starting in release version 0\.5\.1, Apache Hudi changed some of its terminology\. What were formerly views are called queries in later releases\. The following table summarizes the changes between the old and new terms\.


| Old term | New term | 
| --- | --- | 
|  CoW: read optimized view MoR: realtime view  |  Snapshot queries  | 
| Incremental view | Incremental query | 
| MoR read optimized view | Read optimized query | 

### Tables from bootstrap operation<a name="querying-hudi-hudi-dataset-table-types-bootstrap"></a>

Starting in Apache Hudi version 0\.6\.0, the bootstrap operation feature provides better performance with existing Parquet datasets\. Instead of rewriting the dataset, a bootstrap operation can generate metadata only, leaving the dataset in place\. 

You can use Athena to query tables from a bootstrap operation just like other tables based on data in Amazon S3\. In your `CREATE TABLE` statement, specify the Hudi table path in your `LOCATION` clause\. 

For more information about creating Hudi tables using the bootstrap operation in Amazon EMR, see the article [New features from Apache Hudi available in Amazon EMR](http://aws.amazon.com/blogs/big-data/new-features-from-apache-hudi-available-in-amazon-emr/) in the AWS Big Data Blog\.

## Considerations and limitations<a name="querying-hudi-in-athena-considerations-and-limitations"></a>
+ Athena does not support incremental queries\.
+ Athena does not support [CTAS](ctas.md) or [INSERT INTO](insert-into.md) on Hudi data\. If you would like Athena support for writing Hudi datasets, send feedback to athena\-feedback@amazon\.com\.

  For more information about writing Hudi data, see the following resources:
  + [Working with a Hudi dataset](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hudi-work-with-dataset.html) in the [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/)\.
  + [Writing Data](https://hudi.apache.org/docs/0.8.0/writing_data.html) in the Apache Hudi documentation\.
+ Using MSCK REPAIR TABLE on Hudi tables in Athena is not supported\. If you need to load a Hudi table not created in AWS Glue, use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\.

## Video<a name="querying-hudi-videos"></a>

The following video shows how you can use Amazon Athena to query a read\-optimized Apache Hudi dataset in your Amazon S3\-based data lake\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/TVcreqxBaGA/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/TVcreqxBaGA)

## Creating Hudi tables<a name="querying-hudi-in-athena-creating-hudi-tables"></a>

This section provides examples of CREATE TABLE statements in Athena for partitioned and nonpartitioned tables of Hudi data\.

If you have Hudi tables already created in AWS Glue, you can query them directly in Athena\. When you create partitioned Hudi tables in Athena, you must run `ALTER TABLE ADD PARTITION` to load the Hudi data before you can query it\.

### Copy on write \(CoW\) create table examples<a name="querying-hudi-copy-on-write-create-table-examples"></a>

#### Nonpartitioned CoW table<a name="querying-hudi-nonpartitioned-cow-table"></a>

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

#### Partitioned CoW table<a name="querying-hudi-partitioned-cow-table"></a>

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

### Merge on read \(MoR\) create table examples<a name="querying-hudi-merge-on-read-create-table-examples"></a>

Hudi creates two tables in the metastore for MoR: a table for snapshot queries, and a table for read optimized queries\. Both tables are queryable\. In Hudi versions prior to 0\.5\.1, the table for read optimized queries had the name that you specified when you created the table\. Starting in Hudi version 0\.5\.1, the table name is suffixed with `_ro` by default\. The name of the table for snapshot queries is the name that you specified appended with `_rt`\.

#### Nonpartitioned merge on read \(MoR\) table<a name="querying-hudi-nonpartitioned-merge-on-read-table"></a>

The following example creates a nonpartitioned MoR table in Athena for read optimized queries\. Note that read optimized queries use the input format `HoodieParquetInputFormat`\.

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

The following example creates a nonpartitioned MoR table in Athena for snapshot queries\. For snapshot queries, use the input format `HoodieParquetRealtimeInputFormat`\.

```
CREATE EXTERNAL TABLE `nonpartition_mor_rt`(
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
  'org.apache.hudi.hadoop.realtime.HoodieParquetRealtimeInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat' 
LOCATION
  's3://bucket/folder/nonpartition_mor'
```

#### Partitioned merge on read \(MoR\) table<a name="querying-hudi-partitioned-merge-on-read-table"></a>

The following example creates a partitioned MoR table in Athena for read optimized queries\.

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

The following example creates a partitioned MoR table in Athena for snapshot queries\.

```
CREATE EXTERNAL TABLE `partition_mor_rt`(
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
  'org.apache.hudi.hadoop.realtime.HoodieParquetRealtimeInputFormat'
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://bucket/folder/partition_mor'
```

Similarly, the following `ALTER TABLE ADD PARTITION` example adds two partitions to the example `partition_mor_rt` table\.

```
ALTER TABLE partition_mor_rt ADD
  PARTITION (event_type = 'one') LOCATION 's3://bucket/folder/partition_mor/one/'
  PARTITION (event_type = 'two') LOCATION 's3://bucket/folder/partition_mor/two/'
```

## See also<a name="querying-hudi-see-also"></a>

For information about using AWS Glue custom connectors and AWS Glue 2\.0 jobs to create an Apache Hudi table that you can query with Athena, see [Writing to Apache Hudi tables using AWS Glue custom connector](http://aws.amazon.com/blogs/big-data/writing-to-apache-hudi-tables-using-aws-glue-connector/) in the AWS Big Data Blog\.