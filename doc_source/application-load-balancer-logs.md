# Querying Application Load Balancer Logs<a name="application-load-balancer-logs"></a>

An Application Load Balancer is a load balancing option for Elastic Load Balancing that enables traffic distribution in a microservices deployment using containers\. Querying Application Load Balancer logs allows you to see the source of traffic, latency, and bytes transferred to and from Elastic Load Balancing instances and backend applications\.

Before you begin, [enable access logging](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html#enable-access-logging) for Application Load Balancer logs to be saved to your Amazon S3 bucket\.
+  [Creating the Table for ALB Logs](#create-alb-table) 
+  [Adding Partitions to the Table](#create-alb-table-partitions)
+  [Example Queries for ALB logs](#query-alb-logs-examples) 

## Creating the Table for ALB Logs<a name="create-alb-table"></a>

1. Copy and paste the following DDL statement into the Athena console, and modify values in `LOCATION 's3://your_log_bucket/prefix/AWSLogs/your_ID/elasticloadbalancing/region'`\. For a full list of fields present in the ALB logs, see [Access Log Entries](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html#access-log-entry-format)\. 

   Create the `alb_logs` table as follows\.

   ```
    
   CREATE EXTERNAL TABLE IF NOT EXISTS alb_logs (
    type string,
    time string,
    elb string,
    client_ip string,
    client_port int,
    target_ip string,
    target_port int,
    request_processing_time double,
    target_processing_time double,
    response_processing_time double,
    elb_status_code string,
    target_status_code string,
    received_bytes bigint,
    sent_bytes bigint,
    request_verb string,
    request_url string,
    request_proto string,
    user_agent string,
    ssl_cipher string,
    ssl_protocol string,
    target_group_arn string,
    trace_id string,
    domain_name string,
    chosen_cert_arn string
    )
    PARTITIONED BY ( 
    `year` int, 
    `month` int, 
    `day` int)
   ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
   WITH SERDEPROPERTIES (
   'serialization.format' = '1',
   'input.regex' = '([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*)[:\-]([0-9]*) ([-.0-9]*) ([-.0-9]*) ([-.0-9]*) (|[-0-9]*) (-|[-0-9]*) ([-0-9]*) ([-0-9]*) \"([^ ]*) ([^ ]*) (- |[^ ]*)\" (\"[^\"]*\") ([A-Z0-9-]+) ([A-Za-z0-9.-]*) ([^ ]*) (.*) (.*) (.*)' )
   LOCATION 's3://your-alb-logs-directory/AWSLogs/elasticloadbalancing/region';
   ```

1. Run the query in the Athena console\. After the query completes, Athena registers the `alb_logs` table, making the data in it ready for you to issue queries\.

## Adding Partitions to the Table<a name="create-alb-table-partitions"></a>

ALB adds a UTC time prefix in the format YYYY/MM/DD before writing objects to Amazon S3. This prefix creates a logical hierarchy in the bucket, where each forward slash (/) creates a level in the hierarchy\. 
In order to query the data you will need to add partitions to the table using the following syntax:

```
ALTER TABLE alb_logs 
ADD   PARTITION (year=2010,month=04,day=15) 
LOCATION 's3://your-alb-logs-directory/AWSLogs/elasticloadbalancing/region/2010/04/15';
```

We recommand that you automate the partitions creation using a daily [CloudWatch Events Scheduled Expression](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html).

## Example Queries for ALB logs<a name="query-alb-logs-examples"></a>

The following query counts the number of HTTP GET requests received by the load balancer grouped by the client IP address:

```
SELECT COUNT(request_verb) AS
 count,
 request_verb,
 client_ip
FROM alb_logs
GROUP BY request_verb, client_ip
LIMIT 100;
```

Another query shows the URLs visited by Safari browser users:

```
SELECT request_url
FROM alb_logs
WHERE user_agent LIKE '%Safari%'
LIMIT 10;
```

Query all records in a time range (an example for working with time and date):

```
SELECT *
FROM alb_logs
WHERE parse_datetime(time,'yyyy-MM-dd''T''HH:mm:ss.SSSSSSZ')
    BETWEEN parse_datetime('2018-04-15 12:30:00','yyyy-MM-dd HH:mm:ss')
        AND parse_datetime('2018-04-15 13:00:00','yyyy-MM-dd HH:mm:ss')
        AND year=2018
        AND month=04
        AND day=15; 
```
