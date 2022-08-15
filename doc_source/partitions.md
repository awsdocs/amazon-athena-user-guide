# Partitioning data in Athena<a name="partitions"></a>

By partitioning your data, you can restrict the amount of data scanned by each query, thus improving performance and reducing cost\. You can partition your data by any key\. A common practice is to partition the data based on time, often leading to a multi\-level partitioning scheme\. For example, a customer who has data coming in every hour might decide to partition by year, month, date, and hour\. Another customer, who has data coming from many different sources but that is loaded only once per day, might partition by a data source identifier and date\.

Athena can use Apache Hive style partitions, whose data paths contain key value pairs connected by equal signs \(for example, `country=us/...` or `year=2021/month=01/day=26/...`\)\. Thus, the paths include both the names of the partition keys and the values that each path represents\. To load new Hive partitions into a partitioned table, you can use the [MSCK REPAIR TABLE](msck-repair-table.md) command, which works only with Hive\-style partitions\.

Athena can also use non\-Hive style partitioning schemes\. For example, CloudTrail logs and Kinesis Data Firehose delivery streams use separate path components for date parts such as `data/2021/01/26/us/6fc7845e.json`\. For such non\-Hive style partitions, you use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) to add the partitions manually\.

## Considerations and limitations<a name="partitions-considerations-limitations"></a>

When using partitioning, keep in mind the following points:
+ If you query a partitioned table and specify the partition in the `WHERE` clause, Athena scans the data only from that partition\. For more information, see [Table location and partitions](tables-location-format.md#table-location-and-partitions)\.
+ If the partition name is within the `WHERE` clause of the subquery, Athena currently does not filter the partition and instead scans all data from the partitioned table\.
+ If you issue queries against Amazon S3 buckets with a large number of objects and the data is not partitioned, such queries may affect the `GET` request rate limits in Amazon S3 and lead to Amazon S3 exceptions\. To prevent errors, partition your data\. Additionally, consider tuning your Amazon S3 request rates\. For more information, see [Best practices design patterns: Optimizing Amazon S3 performance ](https://docs.aws.amazon.com/AmazonS3/latest/dev/request-rate-perf-considerations.html)\.
+ Partition locations to be used with Athena must use the `s3` protocol \(for example, `s3://DOC-EXAMPLE-BUCKET/folder/`\)\. In Athena, locations that use other protocols \(for example, `s3a://DOC-EXAMPLE-BUCKET/folder/`\) will result in query failures when `MSCK REPAIR TABLE` queries are run on the containing tables\. 
+ Make sure that the Amazon S3 path is in lower case instead of camel case \(for example, `userid` instead of `userId`\)\. If the S3 path is in camel case, `MSCK REPAIR TABLE` doesn't add the partitions to the AWS Glue Data Catalog\. For more information, see [MSCK REPAIR TABLE](msck-repair-table.md)\.
+ Because `MSCK REPAIR TABLE` scans both a folder and its subfolders to find a matching partition scheme, be sure to keep data for separate tables in separate folder hierarchies\. For example, suppose you have data for table A in `s3://table-a-data` and data for table B in `s3://table-a-data/table-b-data`\. If both tables are partitioned by string, `MSCK REPAIR TABLE` will add the partitions for table B to table A\. To avoid this, use separate folder structures like `s3://table-a-data` and `s3://table-b-data` instead\. Note that this behavior is consistent with Amazon EMR and Apache Hive\.
+ If you are using the AWS Glue Data Catalog with Athena, see [AWS Glue endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/glue.html) for service quotas on partitions per account and per table\. 
  + Although Athena supports querying AWS Glue tables that have 10 million partitions, Athena cannot read more than 1 million partitions in a single scan\.
+ To request a partitions quota increase if you are using the AWS Glue Data Catalog, visit the [Service Quotas console for AWS Glue](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/glue/quotas)\.

## Creating and loading a table with partitioned data<a name="partitions-creating-loading"></a>

To create a table that uses partitions, use the `PARTITIONED BY` clause in your [CREATE TABLE](create-table.md) statement\. The `PARTITIONED BY` clause defines the keys on which to partition data, as in the following example\. The `LOCATION` clause specifies the root location of the partitioned data\.

```
CREATE EXTERNAL TABLE users (
first string,
last string,
username string
)
PARTITIONED BY (id string)
STORED AS parquet
LOCATION 's3://DOC-EXAMPLE-BUCKET/folder/'
```

After you create the table, you load the data in the partitions for querying\. For Hive style partitions, you run [MSCK REPAIR TABLE](msck-repair-table.md)\. For non\-Hive style partitions, you use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) to add the partitions manually\.

## Preparing Hive style and non\-Hive style data for querying<a name="partitions-preparing-data"></a>

The following sections show how to prepare Hive style and non\-Hive style data for querying in Athena\.

### Scenario 1: Data stored on Amazon S3 in Hive format<a name="scenario-1-data-already-partitioned-and-stored-on-s3-in-hive-format"></a>

In this scenario, partitions are stored in separate folders in Amazon S3\. For example, here is the partial listing for sample ad impressions output by the [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/ls.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/ls.html) command, which lists the S3 objects under a specified prefix:

```
aws s3 ls s3://elasticmapreduce/samples/hive-ads/tables/impressions/

    PRE dt=2009-04-12-13-00/
    PRE dt=2009-04-12-13-05/
    PRE dt=2009-04-12-13-10/
    PRE dt=2009-04-12-13-15/
    PRE dt=2009-04-12-13-20/
    PRE dt=2009-04-12-14-00/
    PRE dt=2009-04-12-14-05/
    PRE dt=2009-04-12-14-10/
    PRE dt=2009-04-12-14-15/
    PRE dt=2009-04-12-14-20/
    PRE dt=2009-04-12-15-00/
    PRE dt=2009-04-12-15-05/
```

Here, logs are stored with the column name \(dt\) set equal to date, hour, and minute increments\. When you give a DDL with the location of the parent folder, the schema, and the name of the partitioned column, Athena can query data in those subfolders\.

#### Create the table<a name="creating-a-table"></a>

To make a table from this data, create a partition along 'dt' as in the following Athena DDL statement:

```
CREATE EXTERNAL TABLE impressions (
    requestBeginTime string,
    adId string,
    impressionId string,
    referrer string,
    userAgent string,
    userCookie string,
    ip string,
    number string,
    processId string,
    browserCookie string,
    requestEndTime string,
    timers struct<modelLookup:string, requestTime:string>,
    threadId string,
    hostname string,
    sessionId string)
PARTITIONED BY (dt string)
ROW FORMAT  serde 'org.apache.hive.hcatalog.data.JsonSerDe'
LOCATION 's3://elasticmapreduce/samples/hive-ads/tables/impressions/' ;
```

This table uses Hive's native JSON serializer\-deserializer to read JSON data stored in Amazon S3\. For more information about the formats supported, see [Supported SerDes and data formats](supported-serdes.md)\.

#### Run MSCK REPAIR TABLE<a name="run-msck-repair-table"></a>

After you run the `CREATE TABLE` query, run the `MSCK REPAIR TABLE` command in the Athena query editor to load the partitions, as in the following example\.

```
MSCK REPAIR TABLE impressions
```

After you run this command, the data is ready for querying\.

#### Query the data<a name="query-the-data"></a>

Query the data from the impressions table using the partition column\. Here's an example:

```
SELECT dt,impressionid FROM impressions WHERE dt<'2009-04-12-14-00' and dt>='2009-04-12-13-00' ORDER BY dt DESC LIMIT 100
```

This query should show results similar to the following:

```
2009-04-12-13-20    ap3HcVKAWfXtgIPu6WpuUfAfL0DQEc
2009-04-12-13-20    17uchtodoS9kdeQP1x0XThKl5IuRsV
2009-04-12-13-20    JOUf1SCtRwviGw8sVcghqE5h0nkgtp
2009-04-12-13-20    NQ2XP0J0dvVbCXJ0pb4XvqJ5A4QxxH
2009-04-12-13-20    fFAItiBMsgqro9kRdIwbeX60SROaxr
2009-04-12-13-20    V4og4R9W6G3QjHHwF7gI1cSqig5D1G
2009-04-12-13-20    hPEPtBwk45msmwWTxPVVo1kVu4v11b
2009-04-12-13-20    v0SkfxegheD90gp31UCr6FplnKpx6i
2009-04-12-13-20    1iD9odVgOIi4QWkwHMcOhmwTkWDKfj
2009-04-12-13-20    b31tJiIA25CK8eDHQrHnbcknfSndUk
```

### Scenario 2: Data is not partitioned in Hive format<a name="scenario-2-data-is-not-partitioned"></a>

In the following example, the `aws s3 ls` command shows [ELB](elasticloadbalancer-classic-logs.md) logs stored in Amazon S3\. Note how the data layout does not use `key=value` pairs and therefore is not in Hive format\. \(The `--recursive` option for the `aws s3 ls` command specifies that all files or objects under the specified directory or prefix be listed\.\)

```
aws s3 ls s3://athena-examples-myregion/elb/plaintext/ --recursive

2016-11-23 17:54:46   11789573 elb/plaintext/2015/01/01/part-r-00000-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:46    8776899 elb/plaintext/2015/01/01/part-r-00001-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:46    9309800 elb/plaintext/2015/01/01/part-r-00002-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:47    9412570 elb/plaintext/2015/01/01/part-r-00003-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:47   10725938 elb/plaintext/2015/01/01/part-r-00004-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:46    9439710 elb/plaintext/2015/01/01/part-r-00005-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:47          0 elb/plaintext/2015/01/01_$folder$
2016-11-23 17:54:47    9012723 elb/plaintext/2015/01/02/part-r-00006-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:47    7571816 elb/plaintext/2015/01/02/part-r-00007-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:47    9673393 elb/plaintext/2015/01/02/part-r-00008-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:48   11979218 elb/plaintext/2015/01/02/part-r-00009-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:48    9546833 elb/plaintext/2015/01/02/part-r-00010-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:48   10960865 elb/plaintext/2015/01/02/part-r-00011-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:48          0 elb/plaintext/2015/01/02_$folder$
2016-11-23 17:54:48   11360522 elb/plaintext/2015/01/03/part-r-00012-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:48   11211291 elb/plaintext/2015/01/03/part-r-00013-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:48    8633768 elb/plaintext/2015/01/03/part-r-00014-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:49   11891626 elb/plaintext/2015/01/03/part-r-00015-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:49    9173813 elb/plaintext/2015/01/03/part-r-00016-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:49   11899582 elb/plaintext/2015/01/03/part-r-00017-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:49          0 elb/plaintext/2015/01/03_$folder$
2016-11-23 17:54:50    8612843 elb/plaintext/2015/01/04/part-r-00018-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:50   10731284 elb/plaintext/2015/01/04/part-r-00019-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:50    9984735 elb/plaintext/2015/01/04/part-r-00020-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:50    9290089 elb/plaintext/2015/01/04/part-r-00021-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:50    7896339 elb/plaintext/2015/01/04/part-r-00022-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51    8321364 elb/plaintext/2015/01/04/part-r-00023-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51          0 elb/plaintext/2015/01/04_$folder$
2016-11-23 17:54:51    7641062 elb/plaintext/2015/01/05/part-r-00024-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51   10253377 elb/plaintext/2015/01/05/part-r-00025-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51    8502765 elb/plaintext/2015/01/05/part-r-00026-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51   11518464 elb/plaintext/2015/01/05/part-r-00027-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51    7945189 elb/plaintext/2015/01/05/part-r-00028-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51    7864475 elb/plaintext/2015/01/05/part-r-00029-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51          0 elb/plaintext/2015/01/05_$folder$
2016-11-23 17:54:51   11342140 elb/plaintext/2015/01/06/part-r-00030-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:51    8063755 elb/plaintext/2015/01/06/part-r-00031-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:52    9387508 elb/plaintext/2015/01/06/part-r-00032-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:52    9732343 elb/plaintext/2015/01/06/part-r-00033-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:52   11510326 elb/plaintext/2015/01/06/part-r-00034-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:52    9148117 elb/plaintext/2015/01/06/part-r-00035-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:52          0 elb/plaintext/2015/01/06_$folder$
2016-11-23 17:54:52    8402024 elb/plaintext/2015/01/07/part-r-00036-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:52    8282860 elb/plaintext/2015/01/07/part-r-00037-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:52   11575283 elb/plaintext/2015/01/07/part-r-00038-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:53    8149059 elb/plaintext/2015/01/07/part-r-00039-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:53   10037269 elb/plaintext/2015/01/07/part-r-00040-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:53   10019678 elb/plaintext/2015/01/07/part-r-00041-ce65fca5-d6c6-40e6-b1f9-190cc4f93814.txt
2016-11-23 17:54:53          0 elb/plaintext/2015/01/07_$folder$
2016-11-23 17:54:53          0 elb/plaintext/2015/01_$folder$
2016-11-23 17:54:53          0 elb/plaintext/2015_$folder$
```

#### Run ALTER TABLE ADD PARTITION<a name="run-alter-table-add-partition"></a>

Because the data is not in Hive format, you cannot use the `MSCK REPAIR TABLE` command to add the partitions to the table after you create it\. Instead, you can use the [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) command to add each partition manually\. For example, to load the data in s3://athena\-examples\-*myregion*/elb/plaintext/2015/01/01/, you can run the following query\. Note that a separate partition column for each Amazon S3 folder is not required, and that the partition key value can be different from the Amazon S3 key\.

```
ALTER TABLE elb_logs_raw_native_part ADD PARTITION (dt='2015-01-01') location 's3://athena-examples-us-west-1/elb/plaintext/2015/01/01/'
```

If a partition already exists, you receive the error Partition already exists\. To avoid this error, you can use the `IF NOT EXISTS` clause\. For more information, see [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\. To remove a partition, you can use [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md)\. 

## Partition projection<a name="partitions-partition-projection"></a>

To avoid having to manage partitions, you can use partition projection\. Partition projection is an option for highly partitioned tables whose structure is known in advance\. In partition projection, partition values and locations are calculated from table properties that you configure rather than read from a metadata repository\. Because the in\-memory calculations are faster than remote look\-up, the use of partition projection can significantly reduce query runtimes\. 

For more information, see [Partition projection with Amazon Athena](partition-projection.md)\.

## Additional resources<a name="partitions-additional-resources"></a>
+ For information about partitioning options for Kinesis Data Firehose data, see [Amazon Kinesis Data Firehose example](partition-projection-kinesis-firehose-example.md)\.
+ You can automate adding partitions by using the [JDBC driver](connect-with-jdbc.md)\. 
+ You can use CTAS and INSERT INTO to partition a dataset\. For more information, see [Using CTAS and INSERT INTO for ETL and data analysis](ctas-insert-into-etl.md)\.