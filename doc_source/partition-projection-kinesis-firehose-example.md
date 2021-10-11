# Amazon Kinesis Data Firehose Example<a name="partition-projection-kinesis-firehose-example"></a>

**Note**  
This example shows how to use partition projection with the default Kinesis Data Firehose Amazon S3 prefix configuration\. You can use [custom prefixes](https://docs.aws.amazon.com/firehose/latest/dev/s3-prefixes.html) and [dynamic partitioning in Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/dynamic-partitioning.html) to configure other prefixes for outputs that suit your use case\. If you do so, you would have to change the partition projection configuration accordingly\.   
For more information about the dynamic partitioning capabilities in Kinesis Data Firehose, see the AWS What's New post [Introducing Dynamic Partitioning in Amazon Kinesis Data Firehose](http://aws.amazon.com/about-aws/whats-new/2021/08/introducing-dynamic-partitioning-amazon-kinesis-data-firehose/)\.

Kinesis Data Firehose stores data in Amazon S3 in the following path format:

```
s3://bucket/folder/yyyy/MM/dd/HH/file.extension
```

Normally, to use Athena to query Kinesis Data Firehose data without using partition projection, you create a table for Kinesis Data Firehose logs in Athena\. Then you must add partitions to your table in the AWS Glue Data Catalog every hour when Kinesis Data Firehose creates a partition\.

By using partition projection, you can use a one\-time configuration to inform Athena where the partitions reside\. The following `CREATE TABLE` example assumes a start date of 2018\-01\-01 at midnight\. Note the use of `NOW` for the upper boundary of the date range, which allows new data to automatically become queryable at the appropriate UTC time\. 

```
CREATE EXTERNAL TABLE my_table
(
...
)
...
PARTITIONED BY
(
 datehour STRING
)
LOCATION "s3://bucket/table-name/"
TBLPROPERTIES
(
 "projection.enabled" = "true",
 "projection.datehour.type" = "date",
 "projection.datehour.range" = "2018/01/01/00,NOW",
 "projection.datehour.format" = "yyyy/MM/dd/HH",
 "projection.datehour.interval" = "1",
 "projection.datehour.interval.unit" = "HOURS",
 "storage.location.template" = "s3://bucket/table-name/${datehour}"
)
```

Kinesis Data Firehose adds the partition prefix after *table\-name* for you\. In the Kinesis console, *table\-name* appears in the **Custom Prefix** field\.

With this table you can run queries like the following, without having to manually add partitions:

```
SELECT *
FROM my_table
WHERE datehour >= '2018/02/03/00'
AND datehour < '2018/02/03/04'
```