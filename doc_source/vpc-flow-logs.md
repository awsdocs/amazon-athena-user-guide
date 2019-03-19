# Querying Amazon VPC Flow Logs<a name="vpc-flow-logs"></a>

Amazon Virtual Private Cloud flow logs capture information about the IP traffic going to and from network interfaces in a VPC\. Use the logs to investigate network traffic patterns and identify threats and risks across your VPC network\.

Before you begin querying the logs in Athena, [enable VPC flow logs](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html), and configure them to be saved to your Amazon S3 bucket\. After you create the logs, let them run for a few minutes to collect some data\. The logs are created in a GZIP compression format that Athena lets you query directly\. 
+  [Creating the Table for VPC Flow Logs](#create-vpc-logs-table) 
+  [Example Queries for Amazon VPC Flow Logs](#query-examples-vpc-logs) 

## Creating the Table for VPC Flow Logs<a name="create-vpc-logs-table"></a>

### To create the Amazon VPC table<a name="to-create-the-vpc-table"></a>

1. Copy and paste the following DDL statement into the Athena console\. This query specifies `ROW FORMAT DELIMITED` and omits specifying a SerDe\. This means that the query uses the [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md)\. In addition, in this query, fields are terminated by a space\.

1. Modify the `LOCATION 's3://your_log_bucket/prefix/AWSLogs/{subscribe_account_id}/vpcflowlogs/{region_code}/'` to point to the Amazon S3 bucket that contains your log data\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS vpc_flow_logs (
     dt string,
     version int,
     account string,
     interfaceid string,
     sourceaddress string,
     destinationaddress string,
     sourceport int,
     destinationport int,
     protocol int,
     numpackets int,
     numbytes bigint,
     starttime int,
     endtime int,
     action string,
     logstatus string
   )  
   PARTITIONED BY (dt string)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ' '
   LOCATION 's3://your_log_bucket/prefix/AWSLogs/{subscribe_account_id}/vpcflowlogs/{region_code}/'
   TBLPROPERTIES ("skip.header.line.count"="1");
   ```

1. Run the query in Athena console\. After the query completes, Athena registers the `vpc_flow_logs` table, making the data in it ready for you to issue queries\.

1. Create partitions to be able to read the data, as in the following sample query\. This query creates a single partition for a specified date\. Replace the placeholders for date and location as needed\. 
**Note**  
This query creates a single partition only, for a date that you specify\. To automate the process, use a script that runs this query and creates partitions this way for the `year/month/day`, or use AWS Glue Crawler to create partitions for a given Amazon S3 bucket\. For information, see [Scheduling a Crawler to Keep the AWS Glue Data Catalog and Amazon S3 in Sync](glue-best-practices.md#schema-crawlers-schedule)\.

   ```
   ALTER TABLE vpc_flow_logs
   ADD PARTITION (dt='YYYY-MM-dd')
   location 's3://your_log_bucket/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/YYYY/MM/dd';
   ```

## Example Queries for Amazon VPC Flow Logs<a name="query-examples-vpc-logs"></a>

The following query lists all of the rejected TCP connections and uses the newly created date partition column, `dt`, to extract from it the day of the week for which these events occurred\.

This query uses [Date and Time Functions and Operators](https://prestodb.io/docs/0.172/functions/datetime.html)\. It converts values in the `dt` String column to timestamp with the date function `from_iso8601_timestamp(string)`, and extracts the day of the week from timestamp with `day_of_week`\.

```
SELECT day_of_week(from_iso8601_timestamp(dt)) AS
  day,
  dt,
  interfaceid,
  sourceaddress,
  action,
  protocol
FROM vpc_flow_logs
WHERE action = 'REJECT' AND protocol = 6
LIMIT 100;
```

To see which one of your servers is receiving the highest number of HTTPS requests, use this query\. It counts the number of packets received on HTTPS port 443, groups them by destination IP address, and returns the top 10\.

```
SELECT SUM(numpackets) AS
  packetcount,
  destinationaddress
FROM vpc_flow_logs
WHERE destinationport = 443
GROUP BY destinationaddress
ORDER BY packetcount DESC
LIMIT 10;
```

For more information, see the AWS Big Data blog post [Analyzing VPC Flow Logs with Amazon Kinesis Firehose, Athena, and Amazon QuickSight](http://aws.amazon.com/blogs/big-data/analyzing-vpc-flow-logs-with-amazon-kinesis-firehose-amazon-athena-and-amazon-quicksight/)\.