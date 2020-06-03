# Querying Amazon VPC Flow Logs<a name="vpc-flow-logs"></a>

Amazon Virtual Private Cloud flow logs capture information about the IP traffic going to and from network interfaces in a VPC\. Use the logs to investigate network traffic patterns and identify threats and risks across your VPC network\.

Before you begin querying the logs in Athena, [enable VPC flow logs](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html), and configure them to be saved to your Amazon S3 bucket\. After you create the logs, let them run for a few minutes to collect some data\. The logs are created in a GZIP compression format that Athena lets you query directly\. 

When you create a VPC flow log, you can use the default format, or you can specify a custom format\. A custom format is where you specify which fields to return in the flow log, and the order in which they should appear\. For more information, see [Flow Log Records](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html#flow-log-records) in the *Amazon VPC User Guide*\.
+  [Creating the Table for VPC Flow Logs](#create-vpc-logs-table) 
+  [Example Queries for Amazon VPC Flow Logs](#query-examples-vpc-logs) 

## Creating the Table for VPC Flow Logs<a name="create-vpc-logs-table"></a>

The following procedure creates an Amazon VPC table for VPC flow logs that use the default format\. If you create a flow log with a custom format, you must create a table with fields that match the fields that you specified when you created the flow log, in the same order that you specified them\.

**To create the Amazon VPC table**

1. Copy and paste the following DDL statement into the Athena console Query Editor:

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS vpc_flow_logs (
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
   PARTITIONED BY (`date` date)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ' '
   LOCATION 's3://your_log_bucket/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/'
   TBLPROPERTIES ("skip.header.line.count"="1");
   ```

   Note the following points:
   + The query specifies `ROW FORMAT DELIMITED` and omits specifying a SerDe\. This means that the query uses the [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md)\. In this query, fields are terminated by a space\.
   + The `PARTITIONED BY` clause uses the `date` type\. This makes it possible to use mathematical operators in queries to select what's older or newer than a certain date\.
**Note**  
Because `date` is a reserved keyword in DDL statements, it is escaped by backtick characters\. For more information, see [Reserved Keywords](reserved-words.md)\.
   + For a VPC flow log with a custom format, modify the fields to match the fields that you specified when you created the flow log\.

1. Modify the `LOCATION 's3://your_log_bucket/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/'` to point to the Amazon S3 bucket that contains your log data\.

1. Run the query in Athena console\. After the query completes, Athena registers the `vpc_flow_logs` table, making the data in it ready for you to issue queries\.

1. Create partitions to be able to read the data, as in the following sample query\. This query creates a single partition for a specified date\. Replace the placeholders for date and location as needed\. 
**Note**  
This query creates a single partition only, for a date that you specify\. To automate the process, use a script that runs this query and creates partitions this way for the `year/month/day`\.

   ```
   ALTER TABLE vpc_flow_logs
   ADD PARTITION (`date`='YYYY-MM-dd')
   location 's3://your_log_bucket/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/YYYY/MM/dd';
   ```

## Example Queries for Amazon VPC Flow Logs<a name="query-examples-vpc-logs"></a>

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
  interfaceid,
  sourceaddress,
  action,
  protocol
FROM vpc_flow_logs
WHERE action = 'REJECT' AND protocol = 6
LIMIT 100;
```

To see which one of your servers is receiving the highest number of HTTPS requests, use this query\. It counts the number of packets received on HTTPS port 443, groups them by destination IP address, and returns the top 10 from the last week\.

```
SELECT SUM(numpackets) AS
  packetcount,
  destinationaddress
FROM vpc_flow_logs
WHERE destinationport = 443 AND date > current_date - interval '7' day
GROUP BY destinationaddress
ORDER BY packetcount DESC
LIMIT 10;
```

For more information, see the AWS Big Data blog post [Analyzing VPC Flow Logs with Amazon Kinesis Firehose, Athena, and Amazon QuickSight](http://aws.amazon.com/blogs/big-data/analyzing-vpc-flow-logs-with-amazon-kinesis-firehose-amazon-athena-and-amazon-quicksight/)\.
