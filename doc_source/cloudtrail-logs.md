# Querying AWS CloudTrail Logs<a name="cloudtrail-logs"></a>

AWS CloudTrail logs include details about any API calls made to your AWS services, including the console\.

AWS CloudTrail is a service that records AWS API calls and events for AWS accounts\. CloudTrail logs include details about any API calls made to your AWS services, including the console\. CloudTrail generates encrypted log files and stores them in Amazon S3\. For more information, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\. 

Using Athena with CloudTrail logs is a powerful way to enhance your analysis of AWS service activity\. For example, you can use queries to identify trends and further isolate activity by attributes, such as source IP address or user\.

A common application is to use CloudTrail logs to analyze operational activity for security and compliance\. For information about a detailed example, see the AWS Big Data Blog post, [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

You can use Athena to query these log files directly from Amazon S3, specifying the `LOCATION` of log files\. You can do this one of two ways:

+ By creating tables for CloudTrail log files directly from the CloudTrail console\.

+ By manually creating tables for CloudTrail log files in the Athena console\.


+ [Understanding CloudTrail Logs and Athena Tables](#create-cloudtrail-table-understanding)
+ [Creating a Table for CloudTrail Logs in the CloudTrail Console](#create-cloudtrail-table-ct)
+ [Manually Creating the Table for CloudTrail Logs in Athena](#create-cloudtrail-table)
+ [Tips for Querying CloudTrail Logs](#tips-for-querying-cloudtrail-logs)

## Understanding CloudTrail Logs and Athena Tables<a name="create-cloudtrail-table-understanding"></a>

Before you begin creating tables, you should understand a little more about CloudTrail and how it stores data\. This can help you create the tables that you need, whether you create them from the CloudTrail console or from Athena\.

CloudTrail saves logs as JSON text files in compressed gzip format \(\*\.json\.gzip\)\. The location of the log files depends on how you set up trails, the AWS Region or Regions in which you are logging, and other factors\. 

For more information about where logs are stored, the JSON structure, and the record file contents, see the following topics in the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html):

+  [Finding Your CloudTrail Log Files](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-find-log-files.html) 

+  [CloudTrail Log File Examples](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-examples.html) 

+ [CloudTrail Record Contents](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)

+  [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference.html) 

To collect logs and save them to Amazon S3, enable CloudTrail for the console\. For more information, see [Creating a Trail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) in the *AWS CloudTrail User Guide*\.

Note the destination Amazon S3 bucket where you save the logs, as you need it in the next section\.

Replace the `LOCATION` clause with the path to the CloudTrail log location and the set of objects with which to work\. The example uses a `LOCATION` value of logs for a particular account, but you can use the degree of specificity that suits your application\.

For example:

+ To analyze data from multiple accounts, you can roll back the `LOCATION` specifier to indicate all `AWSLogs` by using `LOCATION 's3://MyLogFiles/AWSLogs/`\.

+ To analyze data from a specific date, account, and Region, use `LOCATION `s3://MyLogFiles/123456789012/CloudTrail/us-east-1/2016/03/14/'.` 

Using the highest level in the object hierarchy gives you the greatest flexibility when you query using Athena\.

## Creating a Table for CloudTrail Logs in the CloudTrail Console<a name="create-cloudtrail-table-ct"></a>

You can automatically create tables for querying CloudTrail logs directly from the CloudTrail console\. This is a fairly straightforward method of creating tables, but you can only create tables this way if all of the following conditions are true:

+ You are logged into the CloudTrail console in the same AWS Region where the trail was created\.

+ You are logged in with an IAM user or role that has sufficient permissions to create tables in Athena\. For more information, see [Setting Up](setting-up.md)\.

+ The Amazon S3 bucket that contains the log files for the trail is in the same AWS Region where the trail was created\.

+ Athena is available in the AWS Region where the trail was created\.

If even one of these conditions is not true, you can still create a table but you must do so from Athena\. For more information, see [Manually Creating the Table for CloudTrail Logs in Athena](#create-cloudtrail-table)\.

**To create a table for a CloudTrail trail in the CloudTrail console**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.
**Note**  
Make sure that you are logged in to the Region where the trail was created\.

1. In the navigation pane, choose **Event history**\. 

1. In **Event history**, choose **Run advanced queries in Amazon Athena**\.

1. For **Storage location**, choose the Amazon S3 bucket where log files are stored for the trail to query\.
**Note**  
The list displays all Amazon S3 buckets in the Region that contain CloudTrail log files\. If you have more than one trail in the Region, you might see multiple Amazon S3 buckets\. You can find out what bucket is associated with a trail by going to **Trails** and choosing the trail\. The bucket name is displayed in **Storage location**\.

1. Choose **Create table**\. The table is created with a default name that includes the name of the Amazon S3 bucket\.

## Manually Creating the Table for CloudTrail Logs in Athena<a name="create-cloudtrail-table"></a>

You can manually create tables for CloudTrail log files in the Athena console, and then run queries in Athena\.

**To create a table for a CloudTrail trail in the CloudTrail console**

1. Copy and paste the following DDL statement into the Athena console\.

1. Modify the `s3://CloudTrail_bucket_name/AWSLogs/Account_ID/` to point to the Amazon S3 bucket that contains your logs data\.

1. Verify that fields are listed correctly\. For more information about the full list of fields in a CloudTrail record, see [CloudTrail Record Contents](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\.

   In this example, the fields `requestParameters`, `responseElements`, and `additionalEventData` are included as part of `STRUCT` data type used in JSON\. To get data out of these fields, use `JSON_EXTRACT` functions\. For more information, see [Extracting Data from JSON](extracting-data-from-JSON.md)\.

   ```
   CREATE EXTERNAL TABLE cloudtrail_logs (
   eventversion STRING,
   useridentity STRUCT<
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
   sessionissuer:STRUCT<  
                  type:STRING,
                  principalId:STRING,
                  arn:STRING, 
                  accountId:STRING,
                  userName:STRING>>>,
   eventtime STRING,
   eventsource STRING,
   eventname STRING,
   awsregion STRING,
   sourceipaddress STRING,
   useragent STRING,
   errorcode STRING,
   errormessage STRING,
   requestparameters STRING,
   responseelements STRING,
   additionaleventdata STRING,
   requestid STRING,
   eventid STRING,
   resources ARRAY<STRUCT<
                  ARN:STRING,
                  accountId:STRING,
                  type:STRING>>,
   eventtype STRING,
   apiversion STRING,
   readonly STRING,
   recipientaccountid STRING,
   serviceeventdetails STRING,
   sharedeventid STRING,
   vpcendpointid STRING
   )
   ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
   STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
   OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION 's3://CloudTrail_bucket_name/AWSLogs/Account_ID/';
   ```

1. Run the query in the Athena console\. After the query completes, Athena registers `cloudtrail_logs`, making the data in it ready for you to issue queries\.

## Tips for Querying CloudTrail Logs<a name="tips-for-querying-cloudtrail-logs"></a>

To explore the CloudTrail logs data, use these tips:

+ Before querying the logs, verify that your logs table looks the same as the one in [Manually Creating the Table for CloudTrail Logs in Athena](#create-cloudtrail-table)\. If it is not the first table, delete the existing table using the following command: `DROP TABLE cloudtrail_logs;`\.

+ After you drop the existing table, re\-create it\. For more information, see [Creating the Table for CloudTrail Logs](#create-cloudtrail-table)\.

  Verify that fields in your Athena query are listed correctly\. For information about the full list of fields in a CloudTrail record, see [CloudTrail Record Contents](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\. 

  If your query includes fields in JSON formats, such as `STRUCT`, extract data from JSON\. For more information, see [Extracting Data From JSON](extracting-data-from-JSON.md)\. 

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

+ Modify the earlier query to further explore your data\.

+ To improve performance and prevent long\-running queries, include the `LIMIT` clause to return a specified subset of rows\.

For more information, see the AWS Big Data blog post [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.