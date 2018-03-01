# Querying AWS CloudTrail Logs<a name="cloudtrail-logs"></a>

AWS CloudTrail logs include details about any API calls made to your AWS services, including the console\.

AWS CloudTrail is a service that records AWS API calls and events for AWS accounts\. CloudTrail logs include details about any API calls made to your AWS services, including the console\. CloudTrail generates encrypted log files and stores them in Amazon S3\. You can use Athena to query these log files directly from Amazon S3, specifying the `LOCATION` of log files\. For more information, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\.

Using Athena with CloudTrail logs is a powerful way to enhance your analysis of AWS service activity, for example you can use queries to identify trends and further isolate activity by attribute, such as source IP address or user\.

A common application is to use CloudTrail logs to analyze operational activity for security and compliance\. For a detailed example, see the AWS Big Data Blog post, [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

CloudTrail saves logs as JSON text files in compressed gzip format \(\*\.json\.gzip\)\. The location of log files depends on how you set up trails, the region or regions you are logging, and other factors\. 

For more information about where logs are stored, the JSON structure, and the record file contents, see the following topics in the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html):

+  [Finding Your CloudTrail Log Files](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-find-log-files.html) 

+  [CloudTrail Log File Examples](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-examples.html) 

+ [CloudTrail Record Contents](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)

+  [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference.html) 

To collect logs and save them to Amazon S3, enable CloudTrail for the console\. For more information, see [Creating a Trail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) in the *AWS CloudTrail User Guide*\.

Note the destination Amazon S3 bucket where you save the logs, as you need it in the next section\.

Replace the `LOCATION` clause with the path to the CloudTrail log location and set of objects with which to work\. The example uses a `LOCATION` value of logs for a particular account, but you can use the degree of specificity that suits your application\.

For example:

+ To analyze data from multiple accounts, you can roll back the `LOCATION` specifier to indicate all `AWSLogs` by using `LOCATION 's3://MyLogFiles/AWSLogs/`\.

+ To analyze data from a specific date, account, and region, use `LOCATION `s3://MyLogFiles/123456789012/CloudTrail/us-east-1/2016/03/14/'.` 

Using the highest level in the object hierarchy gives you the greatest flexibility when you query using Athena\.

To query the CloudTrail logs, you use the [CloudTrail SerDe](cloudtrail.md)\.

+  [Creating the Table for CloudTrail Logs](#create-cloudtrail-table) 

+  [Tips for Querying CloudTrail Logs](#tips-for-querying-cloudtrail-logs) 

## Creating the Table for CloudTrail Logs<a name="create-cloudtrail-table"></a>

### To create the CloudTrail table<a name="to-create-the-ct-table"></a>

1. Copy and paste the following DDL statement into the Athena console\.

1. Modify the `s3://cloudtrail_bucket_name/AWSLogs/bucket_ID/` to point to the S3 bucket that contains your logs data\.

   Verify that fields are listed correctly\. For a full list of fields in a CloudTrail record, see [CloudTrail Record Contents](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\.

   In this example, the fields `requestParameters`, `responseElements`, and `additionalEventData` are included as part of `STRUCT` data type used in JSON\. To get data out of these fields, use `JSON_EXTRACT` functions\. For more information, see [Extracting Data From JSON](extracting-data-from-JSON.md)\.

   ```
   CREATE EXTERNAL TABLE cloudtrail_logs (
   eventversion STRING,
   userIdentity STRUCT<
                  type:STRING,
                  principalid:STRING,
                  arn:STRING,
                  accountid:STRING,
                  invokedby:STRING,
                  accesskeyid:STRING,
                  userName:STRING,
   sessioncontext:STRUCT<
   attributes:STRUCT<
                  mfaauthenticated:STRING,
                  creationdate:STRING>,
   sessionIssuer:STRUCT<  
                  type:STRING,
                  principalId:STRING,
                  arn:STRING, 
                  accountId:STRING,
                  userName:STRING>>>,
   eventTime STRING,
   eventSource STRING,
   eventName STRING,
   awsRegion STRING,
   sourceIpAddress STRING,
   userAgent STRING,
   errorCode STRING,
   errorMessage STRING,
   requestParameters STRING,
   responseElements STRING,
   additionalEventData STRING,
   requestId STRING,
   eventId STRING,
   resources ARRAY<STRUCT<
                  ARN:STRING,
                  accountId:STRING,
                  type:STRING>>,
   eventType STRING,
   apiVersion STRING,
   readOnly STRING,
   recipientAccountId STRING,
   serviceEventDetails STRING,
   sharedEventID STRING,
   vpcEndpointId STRING
   )
   ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
   STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
   OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION 's3://cloudtrail_bucket_name/AWSLogs/Account_ID/';
   ```

1. Run the query in Athena console\. After the query completes, Athena registers `cloudtrail_logs`, making the data in it ready for you to issue queries\.

## Tips for Querying CloudTrail Logs<a name="tips-for-querying-cloudtrail-logs"></a>

To explore the CloudTrail logs data, use these tips:

+ Before querying the logs, verify that your logs table looks the same as the one in [Creating the Table for CloudTrail Logs](#create-cloudtrail-table)\. If it isn’t the first table, delete the existing table: `DROP TABLE cloudtrail_logs;`\.

+ Recreate the table\. For more information, see [Creating the Table for CloudTrail Logs](#create-cloudtrail-table)\.

  Verify that fields in your Athena query are listed correctly\. For a full list of fields in a CloudTrail record, see [CloudTrail Record Contents](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\. 

  If your query includes fields in JSON formats, such as `STRUCT`, extract data from JSON\. See [Extracting Data From JSON](extracting-data-from-JSON.md)\. 

  Now you are ready to issue queries against your CloudTrail table\.

+ Start by looking at which IAM users called which API operations and from which source IP addresses\.

+ Use the following basic SQL query as your template\. Paste the query to the Athena console and run it\.

  ```
  SELECT
   useridentity.arn,
   eventname,
   sourceipaddress,
   eventtime
  FROM cloudtrail_logs
  LIMIT 100;
  ```

+ Modify this query further to explore your data\.

+ To improve performance and prevent long\-running queries, include the `LIMIT` clause to return a specified subset of rows\.

For more information, see the AWS Big Data blog post [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.