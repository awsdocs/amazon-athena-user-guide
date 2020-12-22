# Querying AWS CloudTrail Logs<a name="cloudtrail-logs"></a>

AWS CloudTrail is a service that records AWS API calls and events for AWS accounts\. 

CloudTrail logs include details about any API calls made to your AWS services, including the console\. CloudTrail generates encrypted log files and stores them in Amazon S3\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\. 

Using Athena with CloudTrail logs is a powerful way to enhance your analysis of AWS service activity\. For example, you can use queries to identify trends and further isolate activity by attributes, such as source IP address or user\.

A common application is to use CloudTrail logs to analyze operational activity for security and compliance\. For information about a detailed example, see the AWS Big Data Blog post, [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

You can use Athena to query these log files directly from Amazon S3, specifying the `LOCATION` of log files\. You can do this one of two ways:
+ By creating tables for CloudTrail log files directly from the CloudTrail console\.
+ By manually creating tables for CloudTrail log files in the Athena console\.

**Topics**
+ [Understanding CloudTrail Logs and Athena Tables](#create-cloudtrail-table-understanding)
+ [Using the CloudTrail Console to Create an Athena Table for CloudTrail Logs](#create-cloudtrail-table-ct)
+ [Manually Creating the Table for CloudTrail Logs in Athena](#create-cloudtrail-table)
+ [Querying Nested Fields](#cloudtrail-logs-nested-fields)
+ [Example Query](#query-examples-cloudtrail-logs)
+ [Tips for Querying CloudTrail Logs](#tips-for-querying-cloudtrail-logs)

## Understanding CloudTrail Logs and Athena Tables<a name="create-cloudtrail-table-understanding"></a>

Before you begin creating tables, you should understand a little more about CloudTrail and how it stores data\. This can help you create the tables that you need, whether you create them from the CloudTrail console or from Athena\.

CloudTrail saves logs as JSON text files in compressed gzip format \(\*\.json\.gzip\)\. The location of the log files depends on how you set up trails, the AWS Region or Regions in which you are logging, and other factors\. 

For more information about where logs are stored, the JSON structure, and the record file contents, see the following topics in the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html):
+  [Finding Your CloudTrail Log Files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-find-log-files.html) 
+  [CloudTrail Log File Examples](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-examples.html) 
+ [CloudTrail Record Contents](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)
+  [CloudTrail Event Reference](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference.html) 

To collect logs and save them to Amazon S3, enable CloudTrail from the AWS Management Console\. For more information, see [Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) in the *AWS CloudTrail User Guide*\.

Note the destination Amazon S3 bucket where you save the logs\. Replace the `LOCATION` clause with the path to the CloudTrail log location and the set of objects with which to work\. The example uses a `LOCATION` value of logs for a particular account, but you can use the degree of specificity that suits your application\.

For example:
+ To analyze data from multiple accounts, you can roll back the `LOCATION` specifier to indicate all `AWSLogs` by using `LOCATION 's3://MyLogFiles/AWSLogs/`\.
+ To analyze data from a specific date, account, and Region, use `LOCATION `s3://MyLogFiles/123456789012/CloudTrail/us-east-1/2016/03/14/'.` 

Using the highest level in the object hierarchy gives you the greatest flexibility when you query using Athena\.

## Using the CloudTrail Console to Create an Athena Table for CloudTrail Logs<a name="create-cloudtrail-table-ct"></a>

You can create a non\-partitioned Athena table for querying CloudTrail logs directly from the CloudTrail console\. Creating an Athena table from the CloudTrail console requires that you be logged in with an IAM user or role that has sufficient permissions to create tables in Athena\.
+ For information about setting up permissions for Athena, see [Setting Up](setting-up.md)\.
+ For information about creating a table with partitions, see [Manually Creating the Table for CloudTrail Logs in Athena](#create-cloudtrail-table)\.

**To create an Athena table for a CloudTrail trail using the CloudTrail console**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. In the navigation pane, choose **Event history**\. 

1. Do one of the following:
   + If you are using the newer CloudTrail console, choose **Create Athena table**\.  
![\[Choose Create Athena table\]](http://docs.aws.amazon.com/athena/latest/ug/images/cloudtrail-logs-create-athena-table.png)
   + If you are using the older CloudTrail console, choose **Run advanced queries in Amazon Athena**\.  
![\[Choose Run advanced queries in Amazon Athena.\]](http://docs.aws.amazon.com/athena/latest/ug/images/cloudtrail-logs-create-athena-table-older-console.png)

1. For **Storage location**, use the down arrow to select the Amazon S3 bucket where log files are stored for the trail to query\.
**Note**  
To find the name of the bucket that is associated with a trail, choose **Trails** in the CloudTrail navigation pane and view the trail's **S3 bucket** column\. To see the Amazon S3 location for the bucket, choose the link for the bucket in the **S3 bucket** column\. This opens the Amazon S3 console to the CloudTrail bucket location\. 

1. Choose **Create table**\. The table is created with a default name that includes the name of the Amazon S3 bucket\.

## Manually Creating the Table for CloudTrail Logs in Athena<a name="create-cloudtrail-table"></a>

You can manually create tables for CloudTrail log files in the Athena console, and then run queries in Athena\.

**To create an Athena table for a CloudTrail trail using the Athena console**

1. Copy and paste the following DDL statement into the Athena console\. The statement is the same as the one in the CloudTrail console **Create a table in Amazon Athena** dialog box, but adds a `PARTITIONED BY` clause that makes the table partitioned\.

1. Modify `s3://CloudTrail_bucket_name/AWSLogs/Account_ID/CloudTrail/` to point to the Amazon S3 bucket that contains your log data\.

1. Verify that fields are listed correctly\. For more information about the full list of fields in a CloudTrail record, see [CloudTrail Record Contents](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\.

   In this example, the fields `requestparameters`, `responseelements`, and `additionaleventdata` are listed as type `STRING` in the query, but are `STRUCT` data type used in JSON\. Therefore, to get data out of these fields, use `JSON_EXTRACT` functions\. For more information, see [Extracting Data from JSON](extracting-data-from-JSON.md)\. For performance improvements, this example partitions the data by Region, year, month, and day\.

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
   PARTITIONED BY (region string, year string, month string, day string)
   ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
   STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
   OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION 's3://CloudTrail_bucket_name/AWSLogs/Account_ID/CloudTrail/';
   ```

1. Run the query in the Athena console\.

1. Use the [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) command to load the partitions so that you can query them, as in the following example\.

   ```
   ALTER TABLE table_name ADD 
      PARTITION (region='us-east-1',
                 year='2019',
                 month='02',
                 day='01')
      LOCATION 's3://CloudTrail_bucket_name/AWSLogs/Account_ID/CloudTrail/us-east-1/2019/02/01/'
   ```

## Querying Nested Fields<a name="cloudtrail-logs-nested-fields"></a>

Because the `userIdentity` and `resources` fields are nested data types, querying them requires special treatment\.

The `userIdentity` object consists of nested `STRUCT` types\. These can be queried using a dot to separate the fields, as in the following example:

```
SELECT 
    eventsource, 
    eventname,
    useridentity.sessioncontext.attributes.creationdate,
    useridentity.sessioncontext.sessionissuer.arn
FROM cloudtrail_logs
WHERE useridentity.sessioncontext.sessionissuer.arn IS NOT NULL
ORDER BY eventsource, eventname
LIMIT 10
```

The `resources` field is an array of `STRUCT` objects\. For these arrays, use `CROSS JOIN UNNEST` to unnest the array so that you can query its objects\.

The following example returns all rows where the resource ARN ends in `example/datafile.txt`\. For readability, the [replace](https://prestodb.io/docs/0.217/functions/string.html#replace) function removes the initial `arn:aws:s3:::` substring from the ARN\.

```
SELECT 
    awsregion,
    replace(unnested.resources_entry.ARN,'arn:aws:s3:::') as s3_resource,
    eventname,
    eventtime,
    useragent
FROM cloudtrail_logs t
CROSS JOIN UNNEST(t.resources) unnested (resources_entry)
WHERE unnested.resources_entry.ARN LIKE '%example/datafile.txt'
ORDER BY eventtime
```

The following example queries for `DeleteBucket` events\. The query extracts the name of the bucket and the account ID to which the bucket belongs from the `resources` object\.

```
SELECT 
    awsregion,
    replace(unnested.resources_entry.ARN,'arn:aws:s3:::') as deleted_bucket,
    eventtime AS time_deleted,
    useridentity.username, 
    unnested.resources_entry.accountid as bucket_acct_id 
FROM cloudtrail_logs t
CROSS JOIN UNNEST(t.resources) unnested (resources_entry)
WHERE eventname = 'DeleteBucket'
ORDER BY eventtime
```

For more information about unnesting, see [Filtering Arrays](filtering-arrays.md)\.

## Example Query<a name="query-examples-cloudtrail-logs"></a>

The following example shows a portion of a query that returns all anonymous \(unsigned\) requests from the table created for CloudTrail event logs\. This query selects those requests where `useridentity.accountid` is anonymous, and `useridentity.arn` is not specified:

```
SELECT *
FROM cloudtrail_logs
WHERE 
    eventsource = 's3.amazonaws.com' AND 
    eventname in ('GetObject') AND 
    useridentity.accountid LIKE '%ANONYMOUS%' AND 
    useridentity.arn IS NULL AND
    requestparameters LIKE '%[your bucket name ]%';
```

For more information, see the AWS Big Data blog post [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

## Tips for Querying CloudTrail Logs<a name="tips-for-querying-cloudtrail-logs"></a>

To explore the CloudTrail logs data, use these tips:
+ Before querying the logs, verify that your logs table looks the same as the one in [Manually Creating the Table for CloudTrail Logs in Athena](#create-cloudtrail-table)\. If it is not the first table, delete the existing table using the following command: `DROP TABLE cloudtrail_logs;`\.
+ After you drop the existing table, re\-create it\. For more information, see [Creating the Table for CloudTrail Logs](#create-cloudtrail-table)\.

  Verify that fields in your Athena query are listed correctly\. For information about the full list of fields in a CloudTrail record, see [CloudTrail Record Contents](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\. 

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
+ To improve performance, include the `LIMIT` clause to return a specified subset of rows\.