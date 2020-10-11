# Querying Application Load Balancer Logs<a name="application-load-balancer-logs"></a>

An Application Load Balancer is a load balancing option for Elastic Load Balancing that enables traffic distribution in a microservices deployment using containers\. Querying Application Load Balancer logs allows you to see the source of traffic, latency, and bytes transferred to and from Elastic Load Balancing instances and backend applications\. For more information, see the [User Guide for Application Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/)\.

**Topics**
+ [Prerequisites](#application-load-balancer-logs-prerequisites)
+ [Creating the Table for ALB Logs](#create-alb-table)
+ [Example Queries for ALB Logs](#query-alb-logs-examples)

## Prerequisites<a name="application-load-balancer-logs-prerequisites"></a>
+ [Enable access logging](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html#enable-access-logging) so that Application Load Balancer logs can be saved to your Amazon S3 bucket\.

## Creating the Table for ALB Logs<a name="create-alb-table"></a>

1. Copy and paste the following `CREATE TABLE` statement into the Athena console\. Replace the values in `LOCATION 's3://your-alb-logs-directory/AWSLogs/<ACCOUNT-ID>/elasticloadbalancing/<REGION>/'` with those corresponding to your Amazon S3 bucket location\. For information about each field, see [Access Log Entries](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html#access-log-entry-format) in the *User Guide for Application Load Balancers*\. 

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
               chosen_cert_arn string,
               matched_rule_priority string,
               request_creation_time string,
               actions_executed string,
               redirect_url string,
               lambda_error_reason string,
               target_port_list string,
               target_status_code_list string,
               classification string,
               classification_reason string
               )
               ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
               WITH SERDEPROPERTIES (
               'serialization.format' = '1',
               'input.regex' = 
           '([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*)[:-]([0-9]*) ([-.0-9]*) ([-.0-9]*) ([-.0-9]*) (|[-0-9]*) (-|[-0-9]*) ([-0-9]*) ([-0-9]*) \"([^ ]*) ([^ ]*) (- |[^ ]*)\" \"([^\"]*)\" ([A-Z0-9-]+) ([A-Za-z0-9.-]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^\"]*)\" ([-.0-9]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^ ]*)\" \"([^\s]+?)\" \"([^\s]+)\" \"([^ ]*)\" \"([^ ]*)\"')
               LOCATION 's3://your-alb-logs-directory/AWSLogs/<ACCOUNT-ID>/elasticloadbalancing/<REGION>/';
   ```

1. Run the query in the Athena console\. After the query completes, Athena registers the `alb_logs` table, making the data in it ready for you to issue queries\.

## Example Queries for ALB Logs<a name="query-alb-logs-examples"></a>

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

The following example shows how to parse the logs by `datetime`:

```
SELECT client_ip, sum(received_bytes) 
FROM alb_logs_config_us 
WHERE parse_datetime(time,'yyyy-MM-dd''T''HH:mm:ss.SSSSSS''Z') 
     BETWEEN parse_datetime('2018-05-30-12:00:00','yyyy-MM-dd-HH:mm:ss') 
     AND parse_datetime('2018-05-31-00:00:00','yyyy-MM-dd-HH:mm:ss') 
GROUP BY client_ip;
```