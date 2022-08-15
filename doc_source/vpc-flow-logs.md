# Querying Amazon VPC flow logs<a name="vpc-flow-logs"></a>

Amazon Virtual Private Cloud flow logs capture information about the IP traffic going to and from network interfaces in a VPC\. Use the logs to investigate network traffic patterns and identify threats and risks across your VPC network\.

To query your Amazon VPC flow logs, you have two options:

****
+ **Amazon VPC Console** – Use the Athena integration feature in the Amazon VPC Console to generate an AWS CloudFormation template that creates an Athena database, workgroup, and flow logs table with partitioning for you\. The template also creates a set of [predefined flow log queries](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-athena.html#predefined-queries) that you can use to obtain insights about the traffic flowing through your VPC\.

  For information about this approach, see [Query flow logs using Amazon Athena](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-athena.html) in the *Amazon VPC User Guide*\.
+ **Amazon Athena console** – Create your tables and queries directly in the Athena console\. For more information, continue reading this page\.

## Creating and querying tables for VPC flow logs<a name="create-vpc-logs-table"></a>

Before you begin querying the logs in Athena, [enable VPC flow logs](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html), and configure them to be saved to your Amazon S3 bucket\. After you create the logs, let them run for a few minutes to collect some data\. The logs are created in a GZIP compression format that Athena lets you query directly\. 

When you create a VPC flow log, you can use the default format or specify a custom format\. Use a custom format when you want to specify the fields to return in the flow log and the order in which the fields appear\. For more information, see [Flow log records](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html#flow-log-records) in the *Amazon VPC User Guide*\.

### Common considerations<a name="vpc-flow-logs-common-considerations"></a>

When you create tables in Athena for Amazon VPC flow logs, remember the following points:
+ By default, in Athena, Parquet will access columns by name\. For more information, see [Handling schema updates](handling-schema-updates-chapter.md)\.
+ Use the names in the flow log records for the column names in Athena\. The names of the columns in the Athena schema should exactly match the field names in the Amazon VPC flow logs, with the following differences: 
  + Replace the hyphens in the Amazon VPC log field names with underscores in the Athena column names\. In Athena, the only acceptable characters for database names, table names, and column names are lowercase letters, numbers, and the underscore character\. For more information, see [Database, table, and column names](glue-best-practices.md#schema-names)\.
  + Escape the flow log record names that are [reserved keywords](reserved-words.md) in Athena by enclosing them with backticks\. 

### CREATE TABLE statement for Amazon VPC flow logs<a name="vpc-flow-logs-create-table-statement"></a>

The following procedure creates an Amazon VPC table for Amazon VPC flow logs\. If you create a flow log with a custom format, you must create a table with fields that match the fields that you specified when you created the flow log in the same order that you specified them\.

**To create an Athena table for Amazon VPC flow logs**

1. Enter a DDL statement like the following into the Athena console query editor, following the guidelines in the [Common considerations](#vpc-flow-logs-common-considerations) section\. The sample statement creates a table that has the columns for Amazon VPC flow logs versions 2 through 5 as documented in [Flow log records](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html#flow-log-records)\. If you use a different set of columns or order of columns, modify the statement accordingly\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS `vpc_flow_logs` (
     `version` int, 
     `account_id` string, 
     `interface_id` string, 
     `srcaddr` string, 
     `dstaddr` string, 
     `srcport` int, 
     `dstport` int, 
     `protocol` bigint, 
     `packets` bigint, 
     `bytes` bigint, 
     `start` bigint, 
     `end` bigint, 
     `action` string, 
     `log_status` string, 
     `vpc_id` string, 
     `subnet_id` string, 
     `instance_id` string, 
     `tcp_flags` int, 
     `type` string, 
     `pkt_srcaddr` string, 
     `pkt_dstaddr` string, 
     `region` string, 
     `az_id` string, 
     `sublocation_type` string, 
     `sublocation_id` string, 
     `pkt_src_aws_service` string, 
     `pkt_dst_aws_service` string, 
     `flow_direction` string, 
     `traffic_path` int 
   )
   PARTITIONED BY (`date` date)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ' '
   LOCATION 's3://DOC-EXAMPLE-BUCKET/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/'
   TBLPROPERTIES ("skip.header.line.count"="1");
   ```

   Note the following points:
   + The query specifies `ROW FORMAT DELIMITED` and omits specifying a SerDe\. This means that the query uses the [LazySimpleSerDe for CSV, TSV, and custom\-delimited files](lazy-simple-serde.md)\. In this query, fields are terminated by a space\.
   + The `PARTITIONED BY` clause uses the `date` type\. This makes it possible to use mathematical operators in queries to select what's older or newer than a certain date\.
**Note**  
Because `date` is a reserved keyword in DDL statements, it is escaped by backtick characters\. For more information, see [Reserved keywords](reserved-words.md)\.
   + For a VPC flow log with a custom format, modify the fields to match the fields that you specified when you created the flow log\.

1. Modify the `LOCATION 's3://DOC-EXAMPLE-BUCKET/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/'` to point to the Amazon S3 bucket that contains your log data\.

1. Run the query in Athena console\. After the query completes, Athena registers the `vpc_flow_logs` table, making the data in it ready for you to issue queries\.

1. Create partitions to be able to read the data, as in the following sample query\. This query creates a single partition for a specified date\. Replace the placeholders for date and location as needed\. 
**Note**  
This query creates a single partition only, for a date that you specify\. To automate the process, use a script that runs this query and creates partitions this way for the `year/month/day`, or use a `CREATE TABLE` statement that specifies [partition projection](#vpc-flow-logs-partition-projection)\.

   ```
   ALTER TABLE vpc_flow_logs
   ADD PARTITION (`date`='YYYY-MM-dd')
   LOCATION 's3://DOC-EXAMPLE-BUCKET/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/YYYY/MM/dd';
   ```

#### Example queries for the vpc\_flow\_logs table<a name="query-examples-vpc-logs"></a>

Use the query editor in the Athena console to run SQL statements on the table that you create\. You can save the queries, view previous queries, or download query results in CSV format\. In the following examples, replace `vpc_flow_logs` with the name of your table\. Modify the column values and other variables according to your requirements\.

The following example query lists a maximum of 100 flow logs for the date specified\.

```
SELECT * 
FROM vpc_flow_logs 
WHERE date = DATE('2020-05-04') 
LIMIT 100;
```

The following query lists all of the rejected TCP connections and uses the newly created date partition column, `date`, to extract from it the day of the week for which these events occurred\.

```
SELECT day_of_week(date) AS
  day,
  date,
  interface_id,
  srcaddr,
  action,
  protocol
FROM vpc_flow_logs
WHERE action = 'REJECT' AND protocol = 6
LIMIT 100;
```

To see which one of your servers is receiving the highest number of HTTPS requests, use the following query\. It counts the number of packets received on HTTPS port 443, groups them by destination IP address, and returns the top 10 from the last week\.

```
SELECT SUM(packets) AS
  packetcount,
  dstaddr
FROM vpc_flow_logs
WHERE dstport = 443 AND date > current_date - interval '7' day
GROUP BY dstaddr
ORDER BY packetcount DESC
LIMIT 10;
```

### Creating tables for flow logs in Apache Parquet format<a name="vpc-flow-logs-parquet"></a>

The following procedure creates an Amazon VPC table for Amazon VPC flow logs in Apache Parquet format\.

**To create an Athena table for Amazon VPC flow logs in Parquet format**

1. Enter a DDL statement like the following into the Athena console query editor, following the guidelines in the [Common considerations](#vpc-flow-logs-common-considerations) section\. The sample statement creates a table that has the columns for Amazon VPC flow logs versions 2 through 5 as documented in [Flow log records](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html#flow-log-records) in Parquet format, Hive partitioned hourly\. If you do not have hourly partitions, remove `hour` from the `PARTITIONED BY` clause\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS vpc_flow_logs_parquet (
     `version` int, 
     `account_id` string, 
     `interface_id` string, 
     `srcaddr` string, 
     `dstaddr` string, 
     `srcport` int, 
     `dstport` int, 
     `protocol` bigint, 
     `packets` bigint, 
     `bytes` bigint, 
     `start` bigint, 
     `end` bigint, 
     `action` string, 
     `log_status` string, 
     `vpc_id` string, 
     `subnet_id` string, 
     `instance_id` string, 
     `tcp_flags` int, 
     `type` string, 
     `pkt_srcaddr` string, 
     `pkt_dstaddr` string, 
     `region` string, 
     `az_id` string, 
     `sublocation_type` string, 
     `sublocation_id` string, 
     `pkt_src_aws_service` string, 
     `pkt_dst_aws_service` string, 
     `flow_direction` string, 
     `traffic_path` int
   )
   PARTITIONED BY (
     `aws-account-id` string,
     `aws-service` string,
     `aws-region` string,
     `year` string, 
     `month` string, 
     `day` string,
     `hour` string
   )
   ROW FORMAT SERDE 
     'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
   STORED AS INPUTFORMAT 
     'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat' 
   OUTPUTFORMAT 
     'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
   LOCATION
     's3://DOC-EXAMPLE-BUCKET/parquet/AWSLogs/aws-account-id=account_id/aws-service=vpcflowlogs/aws-region=region_code/'
   TBLPROPERTIES (
     'EXTERNAL'='true', 
     'skip.header.line.count'='1'
     )
   ```

1. Modify the sample `LOCATION 's3://DOC-EXAMPLE-BUCKET/parquet/AWSLogs/aws-account-id=account_id/aws-service=vpcflowlogs/aws-region=region_code/' ` to point to the Amazon S3 path that contains your log data\.

1. Run the query in Athena console\.

1. If your data is in Hive\-compatible format, run the following command in the Athena console to update and load the Hive partitions in the metastore\. After the query completes, you can query the data in the `vpc_flow_logs_parquet` table\.

   ```
   MSCK REPAIR TABLE vpc_flow_logs_parquet
   ```

   If you are not using Hive compatible data, run [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) to load the partitions\.

For more information about using Athena to query Amazon VPC flow logs in Parquet format, see the post [Optimize performance and reduce costs for network analytics with VPC Flow Logs in Apache Parquet format](http://aws.amazon.com/blogs/big-data/optimize-performance-and-reduce-costs-for-network-analytics-with-vpc-flow-logs-in-apache-parquet-format/) in the *AWS Big Data Blog*\.

### Creating and querying a table for Amazon VPC flow logs using partition projection<a name="vpc-flow-logs-partition-projection"></a>

Use a `CREATE TABLE` statement like the following to create a table, partition the table, and populate the partitions automatically by using [partition projection](partition-projection.md)\. Replace the table name `test_table_vpclogs` in the example with the name of your table\. Edit the `LOCATION` clause to specify the Amazon S3 bucket that contains your Amazon VPC log data\.

The following `CREATE TABLE` statement is for VPC flow logs delivered in non\-Hive style partitioning format\.

```
CREATE EXTERNAL TABLE IF NOT EXISTS test_table_vpclogs (
`version` int,
`account_id` string,
`interface_id` string,
`srcaddr` string,
`dstaddr` string,
`srcport` int,
`dstport` int,
`protocol` bigint,
`packets` bigint,
`bytes` bigint,
`start` bigint,
`end` bigint,
`action` string,
`log_status` string,
`vpc_id` string,
`subnet_id` string,
`instance_id` string,
`tcp_flags` int,
`type` string,
`pkt_srcaddr` string,
`pkt_dstaddr` string,
`az_id` string,
`sublocation_type` string,
`sublocation_id` string,
`pkt_src_aws_service` string,
`pkt_dst_aws_service` string,
`flow_direction` string,
`traffic_path` int
)
PARTITIONED BY (region string, day string)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ' '
LOCATION 's3://DOC-EXAMPLE-BUCKET/prefix/AWSLogs/{account_id}/vpcflowlogs/'
TBLPROPERTIES
(
"skip.header.line.count"="1",
"projection.enabled" = "true",
"projection.region.type" = "enum",
"projection.region.values" = "us-east-1,us-west-2,ap-south-1,eu-west-1",
"projection.day.type" = "date",
"projection.day.range" = "2021/01/01,NOW",
"projection.day.format" = "yyyy/MM/dd",
"storage.location.template" = "s3://DOC-EXAMPLE-BUCKET/prefix/AWSLogs/{account_id}/vpcflowlogs/${region}/${day}"
)
```

#### Example queries for test\_table\_vpclogs<a name="query-examples-vpc-logs-pp"></a>

The following example queries query the `test_table_vpclogs` created by the preceding `CREATE TABLE` statement\. Replace `test_table_vpclogs` in the queries with the name of your own table\. Modify the column values and other variables according to your requirements\.

To return the first 100 access log entries in chronological order for a specified period of time, run a query like the following\.

```
SELECT *
FROM test_table_vpclogs
WHERE day >= '2021/02/01' AND day < '2021/02/28'
ORDER BY day ASC
LIMIT 100
```

To view which server receives the top ten number of HTTP packets for a specified period of time, run a query like the following\. The query counts the number of packets received on HTTPS port 443, groups them by destination IP address, and returns the top 10 entries from the previous week\.

```
SELECT SUM(packets) AS packetcount, 
       dstaddr
FROM test_table_vpclogs
WHERE dstport = 443
  AND day >= '2021/03/01'
  AND day < '2021/03/31'
GROUP BY dstaddr
ORDER BY packetcount DESC
LIMIT 10
```

To return the logs that were created during a specified period of time, run a query like the following\.

```
SELECT interface_id,
       srcaddr,
       action,
       protocol,
       to_iso8601(from_unixtime(start)) AS start_time,
       to_iso8601(from_unixtime(end)) AS end_time
FROM test_table_vpclogs
WHERE DAY >= '2021/04/01'
  AND DAY < '2021/04/30'
```

To return the access logs for a source IP address between specified time periods, run a query like the following\.

```
SELECT *
FROM test_table_vpclogs
WHERE srcaddr = '10.117.1.22'
  AND day >= '2021/02/01'
  AND day < '2021/02/28'
```

To list rejected TCP connections, run a query like the following\.

```
SELECT day,
       interface_id,
       srcaddr,
       action,
       protocol
FROM test_table_vpclogs
WHERE action = 'REJECT' AND protocol = 6 AND day >= '2021/02/01' AND day < '2021/02/28'
LIMIT 10
```

To return the access logs for the IP address range that starts with `10.117`, run a query like the following\.

```
SELECT *
FROM test_table_vpclogs
WHERE split_part(srcaddr,'.', 1)='10'
  AND split_part(srcaddr,'.', 2) ='117'
```

To return the access logs for a destination IP address between a certain time range, run a query like the following\.

```
SELECT *
FROM test_table_vpclogs
WHERE dstaddr = '10.0.1.14'
  AND day >= '2021/01/01'
  AND day < '2021/01/31'
```

### Additional resources<a name="query-examples-vpc-logs-additional-resources"></a>

For more information about using Athena to analyze VPC flow logs, see the AWS Big Data blog post [Analyzing VPC flow logs using Amazon Athena and Amazon QuickSight](http://aws.amazon.com/blogs/big-data/analyzing-vpc-flow-logs-using-amazon-athena-and-amazon-quicksight/)\.
