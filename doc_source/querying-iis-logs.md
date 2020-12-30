# Querying Internet Information Server \(IIS\) Logs Stored in Amazon S3<a name="querying-iis-logs"></a>

You can use Amazon Athena to query Microsoft Internet Information Services \(IIS\) web server logs stored in your Amazon S3 account\. While IIS uses a [variety](https://docs.microsoft.com/en-us/previous-versions/iis/6.0-sdk/ms525807(v%3Dvs.90)) of log file formats, this topic shows you how to create table schemas to query W3C extended and IIS log file format logs from Athena\.

Because the W3C extended and IIS log file formats use single character delimiters \(spaces and commas, respectively\) and do not have values enclosed in quotation marks, you can use the [LazySimpleSerDe](lazy-simple-serde.md) to create Athena tables for them\.

## W3C Extended Log File Format<a name="querying-iis-logs-w3c-extended-log-file-format"></a>

The [W3C extended](https://docs.microsoft.com/en-us/windows/win32/http/w3c-logging) log file data format has space\-separated fields\. The fields that appear in W3C extended logs are determined by a web server administrator who chooses which log fields to include\. The following example log data has the fields `date, time`, `c-ip`, `s-ip`, `cs-method`, `cs-uri-stem`, `sc-status`, `sc-bytes`, `cs-bytes`, `time-taken`, and `cs-version`\.

```
2020-01-19 22:48:39 203.0.113.5 198.51.100.2 GET /default.html 200 540 524 157 HTTP/1.0
2020-01-19 22:49:40 203.0.113.10 198.51.100.12 GET /index.html 200 420 324 164 HTTP/1.0
2020-01-19 22:50:12 203.0.113.12 198.51.100.4 GET /image.gif 200 324 320 358 HTTP/1.0
2020-01-19 22:51:44 203.0.113.15 198.51.100.16 GET /faq.html 200 330 324 288 HTTP/1.0
```

### Creating a Table in Athena for W3C Extended Logs<a name="querying-iis-logs-creating-a-table-in-athena-for-w3c-extended-logs"></a>

Before you can query your W3C extended logs, you must create a table schema so that Athena can read the log data\.

**To create a table in Athena for W3C extended logs**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Paste a DDL statement like the following into the Athena console, noting the following points:

   1. Add or remove the columns in the example to correspond to the fields in the logs that you want to query\.

   1. Column names in the W3C extended log file format contain hyphens \(`-`\)\. However, in accordance with [Athena naming conventions](tables-databases-columns-names.md), the example `CREATE TABLE` statement replaces them with underscores \(`_`\)\.

   1. To specify the space delimiter, use `FIELDS TERMINATED BY ' '`\.

   1. Modify the values in `LOCATION 's3://bucket-name/w3c-log-folder/'` to point to your W3C extended logs in Amazon S3\.

   ```
   CREATE EXTERNAL TABLE `iis_w3c_logs`( 
     date_col string, 
     time_col string, 
     c_ip string,
     s_ip string,
     cs_method string, 
     cs_uri_stem string, 
     sc_status string,
     sc_bytes string,
     cs_bytes string,
     time_taken string,
     cs_version string
     ) 
   ROW FORMAT DELIMITED  
     FIELDS TERMINATED BY ' '  
   STORED AS INPUTFORMAT  
     'org.apache.hadoop.mapred.TextInputFormat'  
   OUTPUTFORMAT  
     'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat' 
   LOCATION   's3://bucket-name/w3c-log-folder/'
   ```

1. Run the query in the Athena console to register the `iis_w3c_logs` table\. When the query completes, the logs are ready for you to query from Athena\.

### Example W3C Extended Log Select Query<a name="querying-iis-logs-example-w3c-extended-log-select-query"></a>

The following example query selects the date, time, request target, and time taken for the request from the table `iis_w3c_logs`\. The `WHERE` clause filters for cases in which the HTTP method is `GET` and the HTTP status code is `200` \(successful\)\.

```
SELECT date_col, time_col, cs_uri_stem, time_taken
FROM iis_w3c_logs
WHERE cs_method = 'GET' AND sc_status = '200'
```

The following image shows the results of the query in the Athena Query Editor\.

![\[Example query results in Athena of W3C extended log files stored in Amazon S3.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-iis-logs-1.png)

### Combining the Date and Time Fields<a name="querying-iis-logs-example-w3c-extended-log-combining-date-and-time"></a>

The space delimited `date` and `time` fields are separate entries in the log source data, but you can combine them into a timestamp if you want\. Use the [concat\(\)](https://prestodb.io/docs/0.217/functions/string.html#concat) and [date\_parse\(\)](https://prestodb.io/docs/0.217/functions/datetime.html#date_parse) functions in a [SELECT](select.md) or [CREATE TABLE AS SELECT](create-table-as.md) query to concatenate and convert the date and time columns into timestamp format\. The following example uses a CTAS query to create a new table with a `derived_timestamp` column\.

```
CREATE TABLE iis_w3c_logs_w_timestamp AS
SELECT 
  date_parse(concat(date_col,' ', time_col),'%Y-%m-%d %H:%i:%s') as derived_timestamp, 
  c_ip, 
  s_ip, 
  cs_method, 
  cs_uri_stem, 
  sc_status, 
  sc_bytes, 
  cs_bytes, 
  time_taken, 
  cs_version
FROM iis_w3c_logs
```

After the table is created, you can query the new timestamp column directly, as in the following example\.

```
SELECT derived_timestamp, cs_uri_stem, time_taken
FROM iis_w3c_logs_w_timestamp
WHERE cs_method = 'GET' AND sc_status = '200'
```

The following image shows the results of the query\.

![\[W3C extended log file query results on an table with a derived timestamp column.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-iis-logs-1a.png)

## IIS Log File Format<a name="querying-iis-logs-iis-log-file-format"></a>

Unlike the W3C extended format, the [IIS log file format](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc728311(v%3dws.10)) has a fixed set of fields and includes a comma as a delimiter\. The LazySimpleSerDe treats the comma as the delimiter and the space after the comma as the beginning of the next field\.

The following example shows sample data in the IIS log file format\.

```
203.0.113.15, -, 2020-02-24, 22:48:38, W3SVC2, SERVER5, 198.51.100.4, 254, 501, 488, 200, 0, GET, /index.htm, -, 
203.0.113.4, -, 2020-02-24, 22:48:39, W3SVC2, SERVER6, 198.51.100.6, 147, 411, 388, 200, 0, GET, /about.html, -, 
203.0.113.11, -, 2020-02-24, 22:48:40, W3SVC2, SERVER7, 198.51.100.18, 170, 531, 468, 200, 0, GET, /image.png, -, 
203.0.113.8, -, 2020-02-24, 22:48:41, W3SVC2, SERVER8, 198.51.100.14, 125, 711, 868, 200, 0, GET, /intro.htm, -,
```

### Creating a Table in Athena for IIS Log Files<a name="querying-iis-logs-creating-a-table-in-athena-for-iis-log-files"></a>

To query your IIS log file format logs in Amazon S3, you first create a table schema so that Athena can read the log data\.

**To create a table in Athena for IIS log file format logs**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Paste the following DDL statement into the Athena console, noting the following points:

   1. To specify the comma delimiter, use `FIELDS TERMINATED BY ','`\.

   1. Modify the values in LOCATION 's3://*bucket\-name*/*iis\-log\-file\-folder*/' to point to your IIS log format log files in Amazon S3\.

   ```
   CREATE EXTERNAL TABLE `iis_format_logs`(
   client_ip_address string,
   user_name string,
   request_date string,
   request_time string,
   service_and_instance string,
   server_name string,
   server_ip_address string,
   time_taken_millisec string,
   client_bytes_sent string,
   server_bytes_sent string,
   service_status_code string,
   windows_status_code string,
   request_type string,
   target_of_operation string,
   script_parameters string
      )
   ROW FORMAT DELIMITED
     FIELDS TERMINATED BY ','
   STORED AS INPUTFORMAT
     'org.apache.hadoop.mapred.TextInputFormat'
   OUTPUTFORMAT
     'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION
     's3://bucket-name/iis-log-file-folder/'
   ```

1. Run the query in the Athena console to register the `iis_format_logs` table\. When the query completes, the logs are ready for you to query from Athena\.

### Example IIS Log Format Select Query<a name="querying-iis-logs-example-iis-log-format-select-query"></a>

The following example query selects the request date, request time, request target, and time taken in milliseconds from the table `iis_format_logs`\. The `WHERE` clause filters for cases in which the request type is `GET` and the HTTP status code is `200` \(successful\)\. In the query, note that the leading spaces in `' GET'` and `' 200'` are required to make the query successful\. 

```
SELECT request_date, request_time, target_of_operation, time_taken_millisec
FROM iis_format_logs
WHERE request_type = ' GET' AND service_status_code = ' 200'
```

The following image shows the results of the query of the sample data\.

![\[Example query results in Athena of IIS log file format log files stored in Amazon S3.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-iis-logs-2.png)

## NCSA Log File Format<a name="querying-iis-logs-ncsa-log-file-format"></a>

IIS also uses the [NCSA Logging](https://docs.microsoft.com/en-us/windows/win32/http/ncsa-logging) format, which has a fixed number of fields in ASCII text format separated by spaces\. The structure is similar to the common log format used for Apache access logs\. Fields in the NCSA common log data format include the client IP address, client ID \(not typically used\), domain\\user ID, request received timestamp, text of the client request, server status code, and size of the object returned to the client\.

The following example shows data in the NCSA common log format as documented for IIS\.

```
198.51.100.7 - ExampleCorp\Li [10/Oct/2019:13:55:36 -0700] "GET /logo.gif HTTP/1.0" 200 232
198.51.100.14 - AnyCompany\Jorge [24/Nov/2019:10:49:52 -0700] "GET /index.html HTTP/1.1" 200 2165
198.51.100.22 - ExampleCorp\Mateo [27/Dec/2019:11:38:12 -0700] "GET /about.html HTTP/1.1" 200 1287
198.51.100.9 - AnyCompany\Nikki [11/Jan/2020:11:40:11 -0700] "GET /image.png HTTP/1.1" 404 230
198.51.100.2 - ExampleCorp\Ana [15/Feb/2019:10:12:22 -0700] "GET /favicon.ico HTTP/1.1" 404 30
198.51.100.13 - AnyCompany\Saanvi [14/Mar/2019:11:40:33 -0700] "GET /intro.html HTTP/1.1" 200 1608
198.51.100.11 - ExampleCorp\Xiulan [22/Apr/2019:10:51:34 -0700] "GET /group/index.html HTTP/1.1" 200 1344
```

### Creating a Table in Athena for IIS NCSA Logs<a name="querying-iis-logs-ncsa-creating-a-table-in-athena"></a>

For your `CREATE TABLE` statement, you can use the [Grok SerDe](grok-serde.md) and a grok pattern similar to the one for [Apache web server logs](querying-apache-logs.md)\. Unlike Apache logs, the grok pattern uses `%{DATA:user_id}` for the third field instead of `%{USERNAME:user_id}` to account for the presence of the backslash in `domain\user_id`\. For more information about using the Grok SerDe, see [Writing Grok Custom Classifiers](https://docs.aws.amazon.com/glue/latest/dg/custom-classifier.html#custom-classifier-grok) in the *AWS Glue Developer Guide*\.

**To create a table in Athena for IIS NCSA web server logs**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Paste the following DDL statement into the Athena Query Editor\. Modify the values in `LOCATION 's3://bucket-name/iis-ncsa-logs/'` to point to your IIS NCSA logs in Amazon S3\.

   ```
   CREATE EXTERNAL TABLE iis_ncsa_logs(
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
      'input.format'='^%{IPV4:client_ip} %{DATA:client_id} %{DATA:user_id} %{GREEDYDATA:request_received_time} %{QUOTEDSTRING:client_request} %{DATA:server_status} %{DATA: returned_obj_size}$'
      )
   STORED AS INPUTFORMAT
      'org.apache.hadoop.mapred.TextInputFormat'
   OUTPUTFORMAT
      'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION
      's3://bucket-name/iis-ncsa-logs/';
   ```

1. Run the query in the Athena console to register the `iis_ncsa_logs` table\. When the query completes, the logs are ready for you to query from Athena\.

### Example Select Queries for IIS NCSA Logs<a name="querying-iis-logs-ncsa-example-select-queries"></a>

**Example – Filtering for 404 errors**  
The following example query selects the request received time, text of the client request, and server status code from the `iis_ncsa_logs` table\. The `WHERE` clause filters for HTTP status code `404` \(page not found\)\.  

```
SELECT request_received_time, client_request, server_status
FROM iis_ncsa_logs
WHERE server_status = '404'
```
The following image shows the results of the query in the Athena Query Editor\.  

![\[Querying an IIS NCSA log from Athena for HTTP 404 entries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-iis-logs-3.png)

**Example – Filtering for successful requests from a particular domain**  
The following example query selects the user ID, request received time, text of the client request, and server status code from the `iis_ncsa_logs` table\. The `WHERE` clause filters for requests with HTTP status code `200` \(successful\) from users in the `AnyCompany` domain\.  

```
SELECT user_id, request_received_time, client_request, server_status
FROM iis_ncsa_logs
WHERE server_status = '200' AND user_id LIKE 'AnyCompany%'
```
The following image shows the results of the query in the Athena Query Editor\.  

![\[Querying an IIS NCSA log from Athena for HTTP 200 entries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-iis-logs-4.png)