# Amazon Kinesis Data Firehose Example<a name="partition-projection-kinesis-firehose-example"></a>

Kinesis Data Firehose stores data in Amazon S3 in the following path format:

```
s3://bucket/folder/yyyy/MM/dd/HH/file.extension
```

Normally, to use Athena to query Kinesis Data Firehose data without using partition projection, you create a table for Kinesis Data Firehose logs in Athena\. Then you must add partitions to your table in the AWS Glue Data Catalog every hour when Kinesis Data Firehose creates a partition\.

By using partition projection, you can use a one\-time configuration to inform Athena where the partitions reside\. The following `CREATE TABLE` example assumes a start date of 2018\-01\-01 at midnight\. Note the use of `NOW` for the upper boundary of the date range, which allows new data to automatically become queryable at the appropriate UTC time\. 

```
...
PARTITIONED BY
(
 DATEHOUR STRING
)
LOCATION "s3://bucket/prefix/"
TBLPROPERTIES
(
 "projection.enabled" = "true",
 "projection.datehour.type" = "date",
 "projection.datehour.range" = "2018/01/01/00,NOW",
 "projection.datehour.format" = ""yyyy/MM/dd/HH",
 "projection.datehour.interval" = "1",
 "projection.datehour.interval.unit" = "HOURS",
 "storage.location.template" = "s3://bucket/prefix/${datehour}"
)
```