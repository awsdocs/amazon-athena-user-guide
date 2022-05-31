# Querying Amazon CloudFront logs<a name="cloudfront-logs"></a>

You can configure Amazon CloudFront CDN to export Web distribution access logs to Amazon Simple Storage Service\. Use these logs to explore users' surfing patterns across your web properties served by CloudFront\.

Before you begin querying the logs, enable Web distributions access log on your preferred CloudFront distribution\. For information, see [Access logs](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html) in the *Amazon CloudFront Developer Guide*\.

Make a note of the Amazon S3 bucket to which to save these logs\.

**Note**  
This procedure works for the Web distribution access logs in CloudFront\. It does not apply to streaming logs from RTMP distributions\.
+  [Creating the table for CloudFront logs](#create-cloudfront-table) 
+  [Example query for CloudFront logs](#query-examples-cloudfront-logs) 

## Creating the table for CloudFront logs<a name="create-cloudfront-table"></a>

### To create the CloudFront table<a name="to-create-the-cf-table"></a>

1. Copy and paste the following DDL statement into the Query Editor in the Athena console\. Modify the `LOCATION` for the Amazon S3 bucket that stores your logs\. For information about using the Query Editor, see [Getting started](getting-started.md)\.

   This query uses the default SerDe, [LazySimpleSerDe](lazy-simple-serde.md)\. The column `date` is escaped using backticks \(`\) because it is a reserved word in Athena\. For information, see [Reserved keywords](reserved-words.md)\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS default.cloudfront_logs (
     `date` DATE,
     time STRING,
     location STRING,
     bytes BIGINT,
     request_ip STRING,
     method STRING,
     host STRING,
     uri STRING,
     status INT,
     referrer STRING,
     user_agent STRING,
     query_string STRING,
     cookie STRING,
     result_type STRING,
     request_id STRING,
     host_header STRING,
     request_protocol STRING,
     request_bytes BIGINT,
     time_taken FLOAT,
     xforwarded_for STRING,
     ssl_protocol STRING,
     ssl_cipher STRING,
     response_result_type STRING,
     http_version STRING,
     fle_status STRING,
     fle_encrypted_fields INT,
     c_port INT,
     time_to_first_byte FLOAT,
     x_edge_detailed_result_type STRING,
     sc_content_type STRING,
     sc_content_len BIGINT,
     sc_range_start BIGINT,
     sc_range_end BIGINT
   )
   ROW FORMAT DELIMITED 
   FIELDS TERMINATED BY '\t'
   LOCATION 's3://CloudFront_bucket_name/'
   TBLPROPERTIES ( 'skip.header.line.count'='2' )
   ```

1. Run the query in Athena console\. After the query completes, Athena registers the `cloudfront_logs` table, making the data in it ready for you to issue queries\.

## Example query for CloudFront logs<a name="query-examples-cloudfront-logs"></a>

The following query adds up the number of bytes served by CloudFront between June 9 and June 11, 2018\. Surround the date column name with double quotes because it is a reserved word\.

```
SELECT SUM(bytes) AS total_bytes
FROM cloudfront_logs
WHERE "date" BETWEEN DATE '2018-06-09' AND DATE '2018-06-11'
LIMIT 100;
```

To eliminate duplicate rows \(for example, duplicate empty rows\) from the query results, you can use the `SELECT DISTINCT` statement, as in the following example\. 

```
SELECT DISTINCT * 
FROM cloudfront_logs 
LIMIT 10;
```

## Additional resources<a name="cloudfront-logs-additional-resources"></a>

For more information about using Athena to query CloudFront logs, see the following posts from the [AWS big data blog](http://aws.amazon.com/blogs/big-data/)\.

[Easily query AWS service logs using Amazon Athena](http://aws.amazon.com/blogs/big-data/easily-query-aws-service-logs-using-amazon-athena/) \(May 29, 2019\)\.

[Analyze your Amazon CloudFront access logs at scale](http://aws.amazon.com/blogs/big-data/analyze-your-amazon-cloudfront-access-logs-at-scale/) \(December 21, 2018\)\.

[Build a serverless architecture to analyze Amazon CloudFront access logs using AWS Lambda, Amazon Athena, and Amazon Kinesis Data Analytics](http://aws.amazon.com/blogs/big-data/build-a-serverless-architecture-to-analyze-amazon-cloudfront-access-logs-using-aws-lambda-amazon-athena-and-amazon-kinesis-analytics/) \(May 26, 2017\)\.