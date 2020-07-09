# Querying Apache Logs Stored in Amazon S3<a name="querying-apache-logs"></a>

You can use Amazon Athena to query [Apache HTTP Server Log Files](https://httpd.apache.org/docs/2.4/logs.html) stored in your Amazon S3 account\. This topic shows you how to create table schemas to query Apache [Access Log](https://httpd.apache.org/docs/2.4/logs.html#accesslog) files in the common log format\.

Fields in the common log format include the client IP address, client ID, user ID, request received timestamp, text of the client request, server status code, and size of the object returned to the client\.

The following example data shows the Apache common log format\.

```
198.51.100.7 - Li [10/Oct/2019:13:55:36 -0700] "GET /logo.gif HTTP/1.0" 200 232
198.51.100.14 - Jorge [24/Nov/2019:10:49:52 -0700] "GET /index.html HTTP/1.1" 200 2165
198.51.100.22 - Mateo [27/Dec/2019:11:38:12 -0700] "GET /about.html HTTP/1.1" 200 1287
198.51.100.9 - Nikki [11/Jan/2020:11:40:11 -0700] "GET /image.png HTTP/1.1" 404 230
198.51.100.2 - Ana [15/Feb/2019:10:12:22 -0700] "GET /favicon.ico HTTP/1.1" 404 30
198.51.100.13 - Saanvi [14/Mar/2019:11:40:33 -0700] "GET /intro.html HTTP/1.1" 200 1608
198.51.100.11 - Xiulan [22/Apr/2019:10:51:34 -0700] "GET /group/index.html HTTP/1.1" 200 1344
```

## Creating a Table in Athena for Apache Logs<a name="querying-apache-logs-creating-a-table-in-athena"></a>

Before you can query Apache logs stored in Amazon S3, you must create a table schema for Athena so that it can read the log data\. To create an Athena table for Apache logs, you can use the [Grok SerDe](grok-serde.md)\. For more information about using the Grok SerDe, see [Writing Grok Custom Classifiers](https://docs.aws.amazon.com/glue/latest/dg/custom-classifier.html#custom-classifier-grok) in the *AWS Glue Developer Guide*\.

**To create a table in Athena for Apache web server logs**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Paste the following DDL statement into the Athena Query Editor\. Modify the values in `LOCATION 's3://bucket-name/apache-log-folder/'` to point to your Apache logs in Amazon S3\.

   ```
   CREATE EXTERNAL TABLE apache_logs(
     client_ip string,
     client_id string,
     user_id string,
     request_received_time string,
     client_request string,
     server_status string,
     returned_obj_size string
     )
   ROW FORMAT SERDE
      'com.amazonaws.glue.serde.GrokSerDe'
   WITH SERDEPROPERTIES (
      'input.format'='^%{IPV4:client_ip} %{DATA:client_id} %{USERNAME:user_id} %{GREEDYDATA:request_received_time} %{QUOTEDSTRING:client_request} %{DATA:server_status} %{DATA: returned_obj_size}$'
      )
   STORED AS INPUTFORMAT
      'org.apache.hadoop.mapred.TextInputFormat'
   OUTPUTFORMAT
      'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION
      's3://bucket-name/apache-log-folder/';
   ```

1. Run the query in the Athena console to register the `apache_logs` table\. When the query completes, the logs are ready for you to query from Athena\.

### Example Select Queries for Apache Logs<a name="querying-apache-logs-example-select-queries"></a>

**Example – Filtering for 404 errors**  
The following example query selects the request received time, text of the client request, and server status code from the `apache_logs` table\. The `WHERE` clause filters for HTTP status code `404` \(page not found\)\.  

```
SELECT request_received_time, client_request, server_status
FROM apache_logs
WHERE server_status = '404'
```
The following image shows the results of the query in the Athena Query Editor\.  

![\[Querying an Apache log from Athena for HTTP 404 entries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-apache-logs-1.png)

**Example – Filtering for successful requests**  
The following example query selects the user ID, request received time, text of the client request, and server status code from the `apache_logs` table\. The `WHERE` clause filters for HTTP status code `200` \(successful\)\.  

```
SELECT user_id, request_received_time, client_request, server_status
FROM apache_logs
WHERE server_status = '200'
```
The following image shows the results of the query in the Athena Query Editor\.  

![\[Querying an Apache log from Athena for HTTP 200 entries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-apache-logs-2.png)