# CloudTrail SerDe<a name="cloudtrail-serde"></a>

AWS CloudTrail is a service that records AWS API calls and events for Amazon Web Services accounts\. CloudTrail generates encrypted logs and stores them in Amazon S3\. You can use Athena to query these logs directly from Amazon S3, specifying the `LOCATION` of logs\.

To query CloudTrail logs in Athena, create table from the logs and use the CloudTrail SerDe to deserialize the logs data\.

In addition to using the CloudTrail SerDe, instances exist where you need to use a different SerDe or to extract data from JSON\. Certain fields in CloudTrail logs are STRING values that may have a variable data format, which depends on the service\. As a result, the CloudTrail SerDe is unable to predictably deserialize them\. To query the following fields, identify the data pattern and then use a different SerDe, such as the [OpenX JSON SerDe](openx-json-serde.md)\. Alternatively, to get data out of these fields, use `JSON_EXTRACT` functions\. For more information, see [Extracting data from JSON](extracting-data-from-JSON.md)\. 
+  `requestParameters` 
+  `responseElements` 
+  `additionalEventData` 
+  `serviceEventDetails` 

## SerDe name<a name="serde-name"></a>

 `CloudTrail SerDe` 

## Library name<a name="library-name"></a>

 `com.amazon.emr.hive.serde.CloudTrailSerde` 

## Examples<a name="examples"></a>

The following example uses the CloudTrail SerDe on a fictional set of logs to create a table based on them\.

In this example, the fields `requestParameters`, `responseElements`, and `additionalEventData` are actually a kind of `STRUCT` in JSON, but the `CREATE TABLE` statement specifies them as `STRING` so that `json_extract` functions can be used on them to retrieve data\. For more information, see [Extracting data from JSON](extracting-data-from-JSON.md)\.

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

The following query returns the logins that occurred over a 24\-hour period:

```
SELECT
 useridentity.username,
 sourceipaddress,
 eventtime,
 additionaleventdata
FROM default.cloudtrail_logs
WHERE eventname = 'ConsoleLogin'
      AND eventtime >= '2017-02-17T00:00:00Z'
      AND eventtime < '2017-02-18T00:00:00Z';
```

For more information, see [Querying AWS CloudTrail logs](cloudtrail-logs.md)\.