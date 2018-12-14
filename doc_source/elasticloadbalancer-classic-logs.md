# Querying Classic Load Balancer Logs<a name="elasticloadbalancer-classic-logs"></a>

Use Classic Load Balancer logs to analyze and understand traffic patterns to and from Elastic Load Balancing instances and backend applications\. You can see the source of traffic, latency, and bytes transferred\.

Before you begin to analyze the Elastic Load Balancing logs, configure them for saving in the destination Amazon S3 bucket\. For more information, see [Enable Access Logs for Your Classic Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/enable-access-logs.html)\.
+  [Creating the Table for ELB Logs](#create-elb-table) 
+  [Example Queries for ELB Logs](#query-elb-logs-examples) 

## Creating the Table for Elastic Load Balancing Logs<a name="create-elb-table"></a>

Create the table that you can later query\. This table must include the exact location of your Elastic Load Balancing logs in Amazon S3\.

### To create the Elastic Load Balancing table<a name="to-create-the-elb-table"></a>

1. Copy and paste the following DDL statement into the Athena console\.

1. Modify the `LOCATION` S3 bucket to specify the destination of your Elastic Load Balancing logs\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS elb_logs (
    request_timestamp string,
    elb_name string,
    request_ip string,
    request_port int,
    backend_ip string,
    backend_port int,
    request_processing_time double,
    backend_processing_time double,
    response_processing_time double,
    elb_response_code string,
    backend_response_code string,
    received_bytes bigint,
    sent_bytes bigint,
    request_verb string,
    url string,
    protocol string,
    user_agent string,
    ssl_cipher string,
    ssl_protocol string
   )
   ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
   WITH SERDEPROPERTIES (
    'serialization.format' = '1',
    'input.regex' = '([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*)[:\-]([0-9]*) ([-.0-9]*) ([-.0-9]*) ([-.0-9]*) (|[-0-9]*) (-|[-0-9]*) ([-0-9]*) ([-0-9]*) \\\"([^ ]*) ([^ ]*) (- |[^ ]*)\\\" (\"[^\"]*\") ([A-Z0-9-]+) ([A-Za-z0-9.-]*)$' )
   LOCATION 's3://your_log_bucket/prefix/AWSLogs/AWS_account_ID/elasticloadbalancing/';
   ```

1. Run the query in the Athena console\. After the query completes, Athena registers the `elb_logs` table, making the data in it ready for queries\.

## Example Queries for Elastic Load Balancing Logs<a name="query-elb-logs-examples"></a>

Use a query similar to this example\. It lists the backend application servers that returned a `4XX` or `5XX` error response code\. Use the `LIMIT` operator to limit the number of logs to query at a time\.

```
SELECT
 request_timestamp,
 elb_name,
 backend_ip,
 backend_response_code
FROM elb_logs
WHERE backend_response_code LIKE '4%' OR
      backend_response_code LIKE '5%'
LIMIT 100;
```

Use a subsequent query to sum up the response time of all the transactions grouped by the backend IP address and Elastic Load Balancing instance name\.

```
SELECT sum(backend_processing_time) AS
 total_ms,
 elb_name,
 backend_ip
FROM elb_logs WHERE backend_ip <> ''
GROUP BY backend_ip, elb_name
LIMIT 100;
```

For more information, see [Analyzing Data in S3 using Athena](http://aws.amazon.com/blogs/big-data/analyzing-data-in-s3-using-amazon-athena/)\.