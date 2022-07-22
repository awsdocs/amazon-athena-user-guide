# Querying AWS CloudTrail logs<a name="cloudtrail-logs"></a>

AWS CloudTrail is a service that records AWS API calls and events for Amazon Web Services accounts\. 

CloudTrail logs include details about any API calls made to your AWS services, including the console\. CloudTrail generates encrypted log files and stores them in Amazon S3\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\. 

**Note**  
If you want to perform SQL queries on CloudTrail event information across accounts, regions, and dates, consider using CloudTrail Lake\. CloudTrail Lake is an AWS alternative to creating trails that aggregates information from an enterprise into a single, searchable event data store\. Instead of using Amazon S3 bucket storage, it stores events in a data lake, which allows richer, faster queries\. You can use it to create SQL queries that search events across organizations, regions, and within custom time ranges\. Because you perform CloudTrail Lake queries within the CloudTrail console itself, using CloudTrail Lake does not require Athena\. For more information, see the [CloudTrail Lake](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-lake.html) documentation\.

Using Athena with CloudTrail logs is a powerful way to enhance your analysis of AWS service activity\. For example, you can use queries to identify trends and further isolate activity by attributes, such as source IP address or user\.

A common application is to use CloudTrail logs to analyze operational activity for security and compliance\. For information about a detailed example, see the AWS Big Data Blog post, [Analyze security, compliance, and operational activity using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

You can use Athena to query these log files directly from Amazon S3, specifying the `LOCATION` of log files\. You can do this one of two ways:
+ By creating tables for CloudTrail log files directly from the CloudTrail console\.
+ By manually creating tables for CloudTrail log files in the Athena console\.

**Topics**
+ [Understanding CloudTrail logs and Athena tables](#create-cloudtrail-table-understanding)
+ [Using the CloudTrail console to create an Athena table for CloudTrail logs](#create-cloudtrail-table-ct)
+ [Creating the table for CloudTrail logs in Athena using manual partitioning](#create-cloudtrail-table)
+ [Creating the table for CloudTrail logs in Athena using partition projection](#create-cloudtrail-table-partition-projection)
+ [Querying nested fields](#cloudtrail-logs-nested-fields)
+ [Example query](#query-examples-cloudtrail-logs)
+ [Tips for querying CloudTrail logs](#tips-for-querying-cloudtrail-logs)

## Understanding CloudTrail logs and Athena tables<a name="create-cloudtrail-table-understanding"></a>

Before you begin creating tables, you should understand a little more about CloudTrail and how it stores data\. This can help you create the tables that you need, whether you create them from the CloudTrail console or from Athena\.

CloudTrail saves logs as JSON text files in compressed gzip format \(\*\.json\.gzip\)\. The location of the log files depends on how you set up trails, the AWS Region or Regions in which you are logging, and other factors\. 

For more information about where logs are stored, the JSON structure, and the record file contents, see the following topics in the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html):
+  [Finding your CloudTrail log files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-find-log-files.html) 
+  [CloudTrail Log File examples](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-examples.html) 
+ [CloudTrail record contents](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)
+  [CloudTrail event reference](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference.html) 

To collect logs and save them to Amazon S3, enable CloudTrail from the AWS Management Console\. For more information, see [Creating a trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) in the *AWS CloudTrail User Guide*\.

Note the destination Amazon S3 bucket where you save the logs\. Replace the `LOCATION` clause with the path to the CloudTrail log location and the set of objects with which to work\. The example uses a `LOCATION` value of logs for a particular account, but you can use the degree of specificity that suits your application\.

For example:
+ To analyze data from multiple accounts, you can roll back the `LOCATION` specifier to indicate all `AWSLogs` by using `LOCATION 's3://MyLogFiles/AWSLogs/'`\.
+ To analyze data from a specific date, account, and Region, use `LOCATION 's3://MyLogFiles/123456789012/CloudTrail/us-east-1/2016/03/14/'.` 

Using the highest level in the object hierarchy gives you the greatest flexibility when you query using Athena\.

## Using the CloudTrail console to create an Athena table for CloudTrail logs<a name="create-cloudtrail-table-ct"></a>

You can create a non\-partitioned Athena table for querying CloudTrail logs directly from the CloudTrail console\. Creating an Athena table from the CloudTrail console requires that you be logged in with an IAM user or role that has sufficient permissions to create tables in Athena\.

**Note**  
You cannot use the CloudTrail console to create an Athena table for organization trail logs\. Instead, create the table manually using the Athena console so that you can specify the correct storage location\. For information about organization trails, see [Creating a trail for an organization](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-trail-organization.html) in the *AWS CloudTrail User Guide*\.
+ For information about setting up permissions for Athena, see [Setting up](setting-up.md)\.
+ For information about creating a table with partitions, see [Creating the table for CloudTrail logs in Athena using manual partitioning](#create-cloudtrail-table)\.

**To create an Athena table for a CloudTrail trail using the CloudTrail console**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. In the navigation pane, choose **Event history**\. 

1. Choose **Create Athena table**\.  
![\[Choose Create Athena table\]](http://docs.aws.amazon.com/athena/latest/ug/images/cloudtrail-logs-create-athena-table.png)

1. For **Storage location**, use the down arrow to select the Amazon S3 bucket where log files are stored for the trail to query\.
**Note**  
To find the name of the bucket that is associated with a trail, choose **Trails** in the CloudTrail navigation pane and view the trail's **S3 bucket** column\. To see the Amazon S3 location for the bucket, choose the link for the bucket in the **S3 bucket** column\. This opens the Amazon S3 console to the CloudTrail bucket location\. 

1. Choose **Create table**\. The table is created with a default name that includes the name of the Amazon S3 bucket\.

## Creating the table for CloudTrail logs in Athena using manual partitioning<a name="create-cloudtrail-table"></a>

You can manually create tables for CloudTrail log files in the Athena console, and then run queries in Athena\.

**To create an Athena table for a CloudTrail trail using the Athena console**

1. Copy and paste the following DDL statement into the Athena console\. The statement is the same as the one in the CloudTrail console **Create a table in Amazon Athena** dialog box, but adds a `PARTITIONED BY` clause that makes the table partitioned\.

1. Modify `s3://CloudTrail_bucket_name/AWSLogs/Account_ID/CloudTrail/` to point to the Amazon S3 bucket that contains your log data\.

1. Verify that fields are listed correctly\. For more information about the full list of fields in a CloudTrail record, see [CloudTrail record contents](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\.

   In this example, the fields `requestparameters`, `responseelements`, and `additionaleventdata` are listed as type `STRING` in the query, but are `STRUCT` data type used in JSON\. Therefore, to get data out of these fields, use `JSON_EXTRACT` functions\. For more information, see [Extracting data from JSON](extracting-data-from-JSON.md)\. For performance improvements, this example partitions the data by Region, year, month, and day\.

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
   sharedeventid STRING
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

## Creating the table for CloudTrail logs in Athena using partition projection<a name="create-cloudtrail-table-partition-projection"></a>

Because CloudTrail logs have a known structure whose partition scheme you can specify in advance, you can reduce query runtime and automate partition management by using the Athena partition projection feature\. Partition projection automatically adds new partitions as new data is added\. This removes the need for you to manually add partitions by using `ALTER TABLE ADD PARTITION`\. 

The following example `CREATE TABLE` statement automatically uses partition projection on CloudTrail logs from a specified date until the present for a single AWS Region\. In the `LOCATION` and `storage.location.template` clauses, replace the *bucket*, *account\-id*, and *aws\-region* placeholders with correspondingly identical values\. For `projection.timestamp.range`, replace *2020*/*01*/*01* with the starting date that you want to use\. After you run the query successfully, you can query the table\. You do not have to run `ALTER TABLE ADD PARTITION` to load the partitions\.

```
CREATE EXTERNAL TABLE cloudtrail_logs_pp(
    eventVersion STRING,
    userIdentity STRUCT<
        type: STRING,
        principalId: STRING,
        arn: STRING,
        accountId: STRING,
        invokedBy: STRING,
        accessKeyId: STRING,
        userName: STRING,
        sessionContext: STRUCT<
            attributes: STRUCT<
                mfaAuthenticated: STRING,
                creationDate: STRING>,
            sessionIssuer: STRUCT<
                type: STRING,
                principalId: STRING,
                arn: STRING,
                accountId: STRING,
                userName: STRING>>>,
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
    readOnly STRING,
    resources ARRAY<STRUCT<
        arn: STRING,
        accountId: STRING,
        type: STRING>>,
    eventType STRING,
    apiVersion STRING,
    recipientAccountId STRING,
    serviceEventDetails STRING,
    sharedEventID STRING,
    vpcendpointid STRING
  )
PARTITIONED BY (
   `timestamp` string)
ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
  's3://bucket/AWSLogs/account-id/CloudTrail/aws-region'
TBLPROPERTIES (
  'projection.enabled'='true', 
  'projection.timestamp.format'='yyyy/MM/dd', 
  'projection.timestamp.interval'='1', 
  'projection.timestamp.interval.unit'='DAYS', 
  'projection.timestamp.range'='2020/01/01,NOW', 
  'projection.timestamp.type'='date', 
  'storage.location.template'='s3://bucket/AWSLogs/account-id/CloudTrail/aws-region/${timestamp}')
```

For more information about partition projection, see [Partition projection with Amazon Athena](partition-projection.md)\.

## Querying nested fields<a name="cloudtrail-logs-nested-fields"></a>

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

For more information about unnesting, see [Filtering arrays](filtering-arrays.md)\.

## Example query<a name="query-examples-cloudtrail-logs"></a>

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

For more information, see the AWS Big Data blog post [Analyze security, compliance, and operational activity using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

## Tips for querying CloudTrail logs<a name="tips-for-querying-cloudtrail-logs"></a>

To explore the CloudTrail logs data, use these tips:
+ Before querying the logs, verify that your logs table looks the same as the one in [Creating the table for CloudTrail logs in Athena using manual partitioning](#create-cloudtrail-table)\. If it is not the first table, delete the existing table using the following command: `DROP TABLE cloudtrail_logs;`\.
+ After you drop the existing table, re\-create it\. For more information, see [Creating the table for CloudTrail logs in Athena using manual partitioning](#create-cloudtrail-table)\.

  Verify that fields in your Athena query are listed correctly\. For information about the full list of fields in a CloudTrail record, see [CloudTrail record contents](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)\. 

  If your query includes fields in JSON formats, such as `STRUCT`, extract data from JSON\. For more information, see [Extracting data from JSON](extracting-data-from-JSON.md)\. 

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