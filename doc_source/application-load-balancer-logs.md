# Querying Application Load Balancer logs<a name="application-load-balancer-logs"></a>

An Application Load Balancer is a load balancing option for Elastic Load Balancing that enables traffic distribution in a microservices deployment using containers\. Querying Application Load Balancer logs allows you to see the source of traffic, latency, and bytes transferred to and from Elastic Load Balancing instances and backend applications\. For more information, see [Access logs for your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html) in the *User Guide for Application Load Balancers*\.

**Topics**
+ [Prerequisites](#application-load-balancer-logs-prerequisites)
+ [Creating the table for ALB logs](#create-alb-table)
+ [Creating the table for ALB logs in Athena using partition projection](#create-alb-table-partition-projection)
+ [Example queries for ALB logs](#query-alb-logs-examples)

## Prerequisites<a name="application-load-balancer-logs-prerequisites"></a>
+ [Enable access logging](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html#enable-access-logging) so that Application Load Balancer logs can be saved to your Amazon S3 bucket\.
+ A database to hold the table that you will create for Athena\. To create a database, you can use the Athena or AWS Glue console\. For more information, see [Creating databases in Athena](creating-databases.md) in this guide or [Working with databases on the AWS glue console](https://docs.aws.amazon.com/glue/latest/dg/console-databases.html) in the *AWS Glue Developer Guide*\. 

## Creating the table for ALB logs<a name="create-alb-table"></a>

1. Copy and paste the following `CREATE TABLE` statement into the query editor in the Athena console\. For information about getting started with the Athena console, see [Getting started](getting-started.md)\. Replace the values in `LOCATION 's3://your-alb-logs-directory/AWSLogs/<ACCOUNT-ID>/elasticloadbalancing/<REGION>/'` with those corresponding to your Amazon S3 bucket location\. For information about each field, see [Access log entries](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html#access-log-entry-format) in the *User Guide for Application Load Balancers*\. 
**Note**  
The following `CREATE TABLE` statement includes the recently added `classification` and `classification_reason` columns\. To create a table for Application Load Balancer access logs that do not contain these entries, remove these two columns from the `CREATE TABLE` statement and modify the regex accordingly\.

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
               elb_status_code int,
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
           '([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*)[:-]([0-9]*) ([-.0-9]*) ([-.0-9]*) ([-.0-9]*) (|[-0-9]*) (-|[-0-9]*) ([-0-9]*) ([-0-9]*) \"([^ ]*) (.*) (- |[^ ]*)\" \"([^\"]*)\" ([A-Z0-9-_]+) ([A-Za-z0-9.-]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^\"]*)\" ([-.0-9]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^ ]*)\" \"([^\s]+?)\" \"([^\s]+)\" \"([^ ]*)\" \"([^ ]*)\"')
               LOCATION 's3://your-alb-logs-directory/AWSLogs/<ACCOUNT-ID>/elasticloadbalancing/<REGION>/'
   ```

1. Run the query in the Athena console\. After the query completes, Athena registers the `alb_logs` table, making the data in it ready for you to issue queries\.

## Creating the table for ALB logs in Athena using partition projection<a name="create-alb-table-partition-projection"></a>

Because ALB logs have a known structure whose partition scheme you can specify in advance, you can reduce query runtime and automate partition management by using the Athena partition projection feature\. Partition projection automatically adds new partitions as new data is added\. This removes the need for you to manually add partitions by using `ALTER TABLE ADD PARTITION`\. 

The following example `CREATE TABLE` statement automatically uses partition projection on ALB logs from a specified date until the present for a single AWS region\. The statement is based on the example in the previous section but adds `PARTITIONED BY` and `TBLPROPERTIES` clauses to enable partition projection\. In the `LOCATION` and `storage.location.template` clauses, replace the placeholders with values that identify the Amazon S3 bucket location of your ALB logs\. For `projection.day.range`, replace *2022*/*01*/*01* with the starting date that you want to use\. After you run the query successfully, you can query the table\. You do not have to run `ALTER TABLE ADD PARTITION` to load the partitions\.

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
            elb_status_code int,
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
            PARTITIONED BY
            (
             day STRING
            )
            ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
            WITH SERDEPROPERTIES (
            'serialization.format' = '1',
            'input.regex' = 
        '([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*)[:-]([0-9]*) ([-.0-9]*) ([-.0-9]*) ([-.0-9]*) (|[-0-9]*) (-|[-0-9]*) ([-0-9]*) ([-0-9]*) \"([^ ]*) (.*) (- |[^ ]*)\" \"([^\"]*)\" ([A-Z0-9-_]+) ([A-Za-z0-9.-]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^\"]*)\" ([-.0-9]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^ ]*)\" \"([^\s]+?)\" \"([^\s]+)\" \"([^ ]*)\" \"([^ ]*)\"')
            LOCATION 's3://your-alb-logs-directory/AWSLogs/<ACCOUNT-ID>/elasticloadbalancing/<REGION>/'
            TBLPROPERTIES
            (
             "projection.enabled" = "true",
             "projection.day.type" = "date",
             "projection.day.range" = "2022/01/01,NOW",
             "projection.day.format" = "yyyy/MM/dd",
             "projection.day.interval" = "1",
             "projection.day.interval.unit" = "DAYS",
             "storage.location.template" = "s3://your-alb-logs-directory/AWSLogs/<ACCOUNT-ID>/elasticloadbalancing/<REGION>/${day}"
            )
```

For more information about partition projection, see [Partition projection with Amazon Athena](partition-projection.md)\.

## Example queries for ALB logs<a name="query-alb-logs-examples"></a>

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

The following query shows records that have ELB status code values greater than or equal to 500\.

```
SELECT * FROM alb_logs
WHERE elb_status_code >= 500
```

The following example shows how to parse the logs by `datetime`:

```
SELECT client_ip, sum(received_bytes) 
FROM alb_logs
WHERE parse_datetime(time,'yyyy-MM-dd''T''HH:mm:ss.SSSSSS''Z') 
     BETWEEN parse_datetime('2018-05-30-12:00:00','yyyy-MM-dd-HH:mm:ss') 
     AND parse_datetime('2018-05-31-00:00:00','yyyy-MM-dd-HH:mm:ss') 
GROUP BY client_ip;
```

The following query queries the table that uses partition projection for all ALB logs from the specified day\.

```
SELECT * 
FROM alb_logs 
WHERE day = '2022/02/12'
```
+ For more information and examples, see the AWS Knowledge Center article [How do I analyze my Application Load Balancer access logs using Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-analyze-access-logs/)\.
+ For information about Elastic Load Balancing HTTP status codes, see [Troubleshoot your application load balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-troubleshooting.html) in the *User Guide for Application Load Balancers*\.