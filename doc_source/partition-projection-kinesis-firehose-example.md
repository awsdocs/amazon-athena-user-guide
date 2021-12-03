# Amazon Kinesis Data Firehose Example<a name="partition-projection-kinesis-firehose-example"></a>

When you use Kinesis Data Firehose to deliver data to Amazon S3, the default configuration writes objects with keys that look like the following example:

```
s3://bucket/prefix/yyyy/MM/dd/HH/file.extension
```

To create an Athena table that finds the partitions automatically at query time, instead of having to add them to the AWS Glue Data Catalog as new data arrives, you can use partition projection\.

The following `CREATE TABLE` example uses the default Kinesis Data Firehose configuration\.

```
CREATE EXTERNAL TABLE my_ingested_data (
 ...
)
...
PARTITIONED BY (
 datehour STRING
)
LOCATION "s3://DOC-EXAMPLE-BUCKET/prefix/"
TBLPROPERTIES (
 "projection.enabled" = "true",
 "projection.datehour.type" = "date",
 "projection.datehour.format" = "yyyy/MM/dd/HH",
 "projection.datehour.range" = "2021/01/01/00,NOW",
 "projection.datehour.interval" = "1",
 "projection.datehour.interval.unit" = "HOURS",
 "storage.location.template" = "s3://DOC-EXAMPLE-BUCKET/prefix/${datehour}/"
)
```

The `TBLPROPERTIES` clause in the `CREATE TABLE` statement tells Athena the following:
+ Use partition projection when querying the table
+ The partition key `datehour` is of type `date` \(which includes an optional time\)
+ How the dates are formatted
+ The range of date times
+ Where to find the data on Amazon S3\.

When you query the table, Athena calculates the values for `datehour` and uses the storage location template to generate a list of partition locations\.

## Using the `date` Type<a name="partition-projection-kinesis-firehose-example-using-the-date-type"></a>

When you use the `date` type for a projected partition key, you must specify a range\. Because you have no data for dates before the Kinesis Data Firehose delivery stream was created, you can use the date of creation as the start\. And because you do not have data for dates in the future, you can use the special token `NOW` as the end\.

In the `CREATE TABLE` example, the start date is specified as January 1, 2021 at midnight UTC\.

**Note**  
Configure a range that matches your data as closely as possible so that Athena looks only for existing partitions\.

When a query is run on the sample table, Athena uses the conditions on the `datehour` partition key in combination with the range to generate values\. Consider the following query:

```
SELECT *
FROM my_ingested_data
WHERE datehour >= '2020/12/15/00'
AND datehour < '2021/02/03/15'
```

The first condition in the `SELECT` query uses a date that is before the start of the date range specified by the `CREATE TABLE` statement\. Because the partition projection configuration specifies no partitions for dates before January 1, 2021, Athena looks for data only in the following locations, and ignores the earlier dates in the query\.

```
s3://bucket/prefix/2021/01/01/00/
s3://bucket/prefix/2021/01/01/01/
s3://bucket/prefix/2021/01/01/02/
...
s3://bucket/prefix/2021/02/03/12/
s3://bucket/prefix/2021/02/03/13/
s3://bucket/prefix/2021/02/03/14/
```

Similarly, if the query ran at a date and time before February 3, 2021 at 15:00, the last partition would reflect the current date and time, not the date and time in the query condition\.

If you want to query for the most recent data, you can take advantage of the fact that Athena does not generate future dates and specify only a beginning `datehour`, as in the following example\.

```
SELECT *
FROM my_ingested_data
WHERE datehour >= '2021/11/09/00'
```

## Choosing Partition Keys<a name="partition-projection-kinesis-firehose-example-choosing-partition-keys"></a>

You can specify how partition projection maps the partition locations to partition keys\. In the `CREATE TABLE` example, the date and hour were combined into one partition key called datehour, but other schemes are possible\. For example, you could also configure a table with separate partition keys for the year, month, day, and hour\.

Queries can be easier to read when there are separate conditions on the day and hour\. With that in mind, the following `CREATE TABLE` example separates the date from the hour\.

```
CREATE EXTERNAL TABLE my_ingested_data2 (
 ...
)
...
PARTITIONED BY (
 day STRING,
 hour INT
)
LOCATION "s3://DOC-EXAMPLE-BUCKET/prefix/"
TBLPROPERTIES (
 "projection.enabled" = "true",
 "projection.day.type" = "date",
 "projection.day.format" = "yyyy/MM/dd",
 "projection.day.range" = "2021/01/01,NOW",
 "projection.day.interval" = "1",
 "projection.day.interval.unit" = "DAYS",
 "projection.hour.type" = "integer",
 "projection.hour.range" = "0,23",
 "projection.hour.digits" = "2",
 "storage.location.template" = "s3://DOC-EXAMPLE-BUCKET/prefix/${day}/${hour}/"
)
```

In the example `CREATE TABLE` statement, the hour is a separate partition key, configured as an integer\. The configuration for the hour partition key specifies the range 0 to 23, and that the hour should be formatted with two digits when Athena generates the partition locations\.

A query for the `my_ingested_data2` table might look like this:

```
SELECT *
FROM my_ingested_data2
WHERE day = '2021/11/09'
AND hour > 3
```

### Partition Key Types and Partition Projection Types<a name="partition-projection-kinesis-firehose-example-partition-key-types-and-partition-projection-types"></a>

Note that `datehour` key in the first `CREATE TABLE` example is configured as `date` in the partition projection configuration, but the type of the partition key is `string`\. The same is true for `day` in the second example\. The types in the partition projection configuration only tell Athena how to format the values when it generates the partition locations\. The types that you specify do not change the type of the partition key — in queries, `datehour` and `day` are of type `string`\.

When a query includes a condition like `day = '2021/11/09'`, Athena parses the string on the right side of the expression using the date format specified in the partition projection configuration\. After Athena verifies that the date is within the configured range, it uses the date format again to insert the date as a string into the storage location template\.

Similarly, for a query condition like `day > '2021/11/09'`, Athena parses the right side and generates a list of all matching dates within the configured range\. It then uses the date format to insert each date into the storage location template to create the list of partition locations\.

Writing the same condition as `day > '2021-11-09'` or `day > DATE '2021-11-09'` does not work\. In the first case, the date format does not match \(note the hyphens instead of the forward slashes\), and in the second case, the data types do not match\.

## Using Custom Prefixes and Dynamic Partitioning<a name="partition-projection-kinesis-firehose-example-using-custom-prefixes-and-dynamic-partitioning"></a>

Kinesis Data Firehose can be configured with [custom prefixes](https://docs.aws.amazon.com/firehose/latest/dev/s3-prefixes.html) and [dynamic partitioning](https://docs.aws.amazon.com/firehose/latest/dev/dynamic-partitioning.html)\. Using these features, you can configure the Amazon S3 keys and set up partitioning schemes that better support your use case\. You can also use partition projection with these partitioning schemes and configure them accordingly\.

For example, you could use the custom prefix feature to get Amazon S3 keys that have ISO formatted dates instead of the default `yyyy/MM/dd/HH` scheme\.

You can also combine custom prefixes with dynamic partitioning to extract a property like `customer_id` from Kinesis Data Firehose messages, as in the following example\.

```
prefix/!{timestamp:yyyy}-!{timestamp:MM}-!{timestamp:dd}/!{partitionKeyFromQuery:customer_id}/
```

With that Amazon S3 prefix, the Kinesis Data Firehose delivery stream would write objects to keys such as `s3://bucket/prefix/2021-11-01/customer-1234/file.extension`\. For a property like `customer_id`, where the values may not be known in advance, you can use the partition projection type `injected` and use a `CREATE TABLE` statement like the following:

```
CREATE EXTERNAL TABLE my_ingested_data3 (
 ...
)
...
PARTITIONED BY (
 day STRING,
 customer_id STRING
)
LOCATION "s3://DOC-EXAMPLE-BUCKET/prefix/"
TBLPROPERTIES (
 "projection.enabled" = "true",
 "projection.day.type" = "date",
 "projection.day.format" = "yyyy-MM-dd",
 "projection.day.range" = "2021-01-01,NOW",
 "projection.day.interval" = "1",
 "projection.day.interval.unit" = "DAYS",
 "projection.customer_id.type" = "injected",
 "storage.location.template" = "s3://DOC-EXAMPLE-BUCKET/prefix/${day}/${customer_id}/"
)
```

When you query a table that has a partition key of type `injected`, your query must include a value for that partition key\. A query for the `my_ingested_data3` table might look like this:

```
SELECT *
FROM my_ingested_data3
WHERE day BETWEEN '2021-11-01' AND '2021-11-30'
AND customer_id = 'customer-1234'
```

### ISO Formatted Dates<a name="partition-projection-kinesis-firehose-example-iso-formatted-dates"></a>

Because the values for the `day` partition key are ISO formatted, you can also use the `DATE` type for the day partition key instead of `STRING`, as in the following example:

```
PARTITIONED BY (day DATE, customer_id STRING)
```

When you query, this strategy allows you to use date functions on the partition key without parsing or casting, as in the following example:

```
SELECT *
FROM my_ingested_data3
WHERE day > CURRENT_DATE - INTERVAL '7' DAY
AND customer_id = 'customer-1234'
```