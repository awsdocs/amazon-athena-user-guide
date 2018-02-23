# Querying Amazon VPC Flow Logs<a name="vpc-flow-logs"></a>

Amazon Virtual Private Cloud flow logs capture information about the IP traffic going to and from network interfaces in a VPC\. Use the logs to investigate network traffic patterns and identify threats and risks across your VPC network\.

Before you begin querying the logs in Athena, [enable VPC flow logs](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html) and [export log data to Amazon S3](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3ExportTasksConsole.html)\. After you create the logs, let them run for a few minutes to collect some data\.

+  [Creating the Table for VPC Flow Logs](#create-vpc-logs-table) 

+  [Example Queries for Amazon VPC Flow Logs](#query-examples-vpc-logs) 

## Creating the Table for VPC Flow Logs<a name="create-vpc-logs-table"></a>

### To create the Amazon VPC table<a name="to-create-the-vpc-table"></a>

1. Copy and paste the following DDL statement into the Athena console\.

1. Modify the `LOCATION 's3://your_log_bucket/prefix/'` to point to the S3 bucket that contains your log data\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS vpc_flow_logs (
     ts string,
     version int,
     account string,
     interfaceid string,
     sourceaddress string,
     destinationaddress string,
     sourceport int,
     destinationport int,
     protocol int,
     numpackets int,
     numbytes int,
     starttime string,
     endtime string,
     action string,
     logstatus string
   )
   ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
   WITH SERDEPROPERTIES
    ( "input.regex" = "^([^ ]+)\\s+([0-9]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([0-9]+)\\s+([0-9]+)\\s+([0-9]+)\\s+([0-9]+)\\s+([0-9]+)\\s+([0-9]+)\\s+([0-9]+)\\s+([^ ]+)\\s+([^ ]+)$" )
   LOCATION 's3://your_log_bucket/prefix/';
   ```

1. Run the query in Athena console\. After the query completes, Athena registers the `vpc_flow_logs` table, making the data in it ready for you to issue queries\.

## Example Queries for Amazon VPC Flow Logs<a name="query-examples-vpc-logs"></a>

The following query lists all of the rejected TCP connections\. The query uses a `datetime` function to convert the timestamp field `ts`, and extracts only the day of the week for which these events occurred\.

```
SELECT day_of_week(from_iso8601_timestamp(ts)) AS
  day,
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