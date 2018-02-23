# Querying Amazon CloudFront Logs<a name="cloudfront-logs"></a>

You can configure Amazon CloudFront CDN to export Web distribution access logs to Amazon Simple Storage Service\. Use these logs to explore users’ surfing patterns across your web properties served by CloudFront\.

Before you begin querying the logs, enable Web distributions access log on your preferred CloudFront distribution\. For information, see [Access Logs](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html) in the *Amazon CloudFront Developer Guide*\.

Make a note of the Amazon S3 bucket to which to save these logs\.

**Note**  
This procedure works for the Web distribution access logs in CloudFront\. It does not apply to streaming logs from RTMP distributions\.

+  [Creating the Table for CloudFront Logs](#create-cloudfront-table) 

+  [Example Query for CloudFront logs](#query-examples-cloudfront-logs) 

## Creating the Table for CloudFront Logs<a name="create-cloudfront-table"></a>

### To create the CloudFront table<a name="to-create-the-cf-table"></a>

1. Copy and paste the following DDL statement into the Athena console\. Modify the `LOCATION` for the S3 bucket that stores your logs\.

   This query uses the [LazySimpleSerDe](lazy-simple-serde.md) by default and it is omitted\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS default.cloudfront_logs (
     `date` DATE,
     time STRING,
     location STRING,
     bytes BIGINT,
     requestip STRING,
     method STRING,
     host STRING,
     uri STRING,
     status INT,
     referrer STRING,
     useragent STRING,
     querystring STRING,
     cookie STRING,
     resulttype STRING,
     requestid STRING,
     hostheader STRING,
     requestprotocol STRING,
     requestbytes BIGINT,
     timetaken FLOAT,
     xforwardedfor STRING,
     sslprotocol STRING,
     sslcipher STRING,
     responseresulttype STRING,
     httpversion STRING,
     filestatus STRING,
     encryptedfields INT
   )
   ROW FORMAT DELIMITED 
   FIELDS TERMINATED BY '\t'
   LOCATION 's3://your_log_bucket/prefix/'
   TBLPROPERTIES ( 'skip.header.line.count'='2' )
   ```

1. Run the query in Athena console\. After the query completes, Athena registers the `cloudfront_logs` table, making the data in it ready for you to issue queries\.

## Example Query for CloudFront logs<a name="query-examples-cloudfront-logs"></a>

The following query adds up the number of bytes served by CloudFront between June 9 and June 11, 2017\. Surround the date column name with double quotes because it is a reserved word\.

```
SELECT SUM(bytes) AS total_bytes
FROM cloudfront_logs
WHERE "date" BETWEEN DATE '2017-06-09' AND DATE '2017-06-11'
LIMIT 100;
```

In some cases, you need to eliminate empty values from the results of `CREATE TABLE` query for CloudFront\. To do so, run `SELECT DISTINCT * FROM cloudfront_logs LIMIT 10;` 

For more information, see the AWS Big Data Blog post [Build a Serverless Architecture to Analyze Amazon CloudFront Access Logs Using AWS Lambda, Amazon Athena, and Amazon Kinesis Analytics](http://aws.amazon.com/blogs/big-data/build-a-serverless-architecture-to-analyze-amazon-cloudfront-access-logs-using-aws-lambda-amazon-athena-and-amazon-kinesis-analytics/)\.