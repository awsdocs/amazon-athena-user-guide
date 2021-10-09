# Querying AWS WAF Logs<a name="waf-logs"></a>

AWS WAF logs include information about the traffic that is analyzed by your web ACL, such as the time that AWS WAF received the request from your AWS resource, detailed information about the request, and the action for the rule that each request matched\.

You can enable access logging for AWS WAF logs and save them to Amazon S3\. Make a note of the Amazon S3 bucket to which you save these logs, and you can create an Athena table for them and query them in Athena\. 

For more information about enabling AWS WAF logs and about the log record structure, see [Logging web ACL traffic information](https://docs.aws.amazon.com/waf/latest/developerguide/logging.html) in the *AWS WAF Developer Guide*\.

For an example of how to aggregate AWS WAF logs into a central data lake repository and query them with Athena, see the AWS Big Data Blog post [Analyzing AWS WAF logs with Amazon ES, Amazon Athena, and Amazon QuickSight](http://aws.amazon.com/blogs/big-data/analyzing-aws-waf-logs-with-amazon-es-amazon-athena-and-amazon-quicksight/)\.

**Topics**
+ [Creating the Table for AWS WAF Logs](#create-waf-table)
+ [Creating the Table for AWS WAF Logs in Athena Using Partition Projection](#create-waf-table-partition-projection)
+ [Example Queries for AWS WAF Logs](#query-examples-waf-logs)

## Creating the Table for AWS WAF Logs<a name="create-waf-table"></a>

### To create the AWS WAF table<a name="to-create-the-waf-table"></a>

1. Copy and paste the following DDL statement into the Athena console\. Modify the `LOCATION` for the Amazon S3 bucket that stores your logs\.

   This query uses the [OpenX JSON SerDe](json-serde.md#openx-json-serde)\. The table format and the SerDe are suggested by the AWS Glue crawler when it analyzes AWS WAF logs\.
**Note**  
The SerDe expects each JSON document to be on a single line of text with no line termination characters separating the fields in the record\. If the JSON text is in pretty print format, you may receive an error message like HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object or HIVE\_CURSOR\_ERROR: JsonParseException: Unexpected end\-of\-input: expected close marker for OBJECT when you attempt to query the table after you create it\. For more information, see [JSON Data Files](https://github.com/rcongiu/Hive-JSON-Serde#json-data-files) in the OpenX SerDe documentation on GitHub\. 

   ```
   CREATE EXTERNAL TABLE `waf_logs`(
     `timestamp` bigint,
     `formatversion` int,
     `webaclid` string,
     `terminatingruleid` string,
     `terminatingruletype` string,
     `action` string,
     `terminatingrulematchdetails` array<
                                     struct<
                                       conditiontype:string,
                                       location:string,
                                       matcheddata:array<string>
                                           >
                                        >,
     `httpsourcename` string,
     `httpsourceid` string,
     `rulegrouplist` array<
                        struct<
                           rulegroupid:string,
                           terminatingrule:struct<
                              ruleid:string,
                              action:string,
                              rulematchdetails:string
                                                  >,
                           nonterminatingmatchingrules:array<
                                                          struct<
                                                             ruleid:string,
                                                             action:string,
                                                             rulematchdetails:array<
                                                                  struct<
                                                                     conditiontype:string,
                                                                     location:string,
                                                                     matcheddata:array<string>
                                                                        >
                                                                     >
                                                                  >
                                                               >,
                           excludedrules:array<
                                            struct<
                                               ruleid:string,
                                               exclusiontype:string
                                                  >
                                               >
                              >
                          >,
     `ratebasedrulelist` array<
                           struct<
                             ratebasedruleid:string,
                             limitkey:string,
                             maxrateallowed:int
                                 >
                              >,
     `nonterminatingmatchingrules` array<
                                     struct<
                                       ruleid:string,
                                       action:string
                                           >
                                        >,
     `requestheadersinserted` string,
     `responsecodesent` string,
     `httprequest` struct<
                         clientip:string,
                         country:string,
                         headers:array<
                                   struct<
                                     name:string,
                                     value:string
                                         >
                                      >,
                         uri:string,
                         args:string,
                         httpversion:string,
                         httpmethod:string,
                         requestid:string
                         >,
     `labels` array<
                struct<
                  name:string
                      >
                     >
   )
   ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
   WITH SERDEPROPERTIES (
    'paths'='action,formatVersion,httpRequest,httpSourceId,httpSourceName,labels,nonTerminatingMatchingRules,rateBasedRuleList,requestHeadersInserted,responseCodeSent,ruleGroupList,terminatingRuleId,terminatingRuleMatchDetails,terminatingRuleType,timestamp,webaclId')
   STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'
   OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION 's3://athenawaflogs/WebACL/'
   ```

1. Run the `CREATE EXTERNAL TABLE` statement in the Athena console Query Editor\. This registers the `waf_logs` table and makes the data in it available for queries from Athena\.

## Creating the Table for AWS WAF Logs in Athena Using Partition Projection<a name="create-waf-table-partition-projection"></a>

Because AWS WAF logs have a known structure whose partition scheme you can specify in advance, you can reduce query runtime and automate partition management by using the Athena partition projection feature\. Partition projection automatically adds new partitions as new data is added\. This removes the need for you to manually add partitions by using `ALTER TABLE ADD PARTITION`\. 

The following example `CREATE TABLE` statement automatically uses partition projection on AWS WAF logs from a specified date until the present for a single AWS region\. In the `LOCATION` and `storage.location.template` clauses, replace the *bucket* and *folder* placeholders with values that identify the Amazon S3 bucket location of your AWS WAF logs\. For `projection.day.range`, replace *2021*/*01*/*01* with the starting date that you want to use\. After you run the query successfully, you can query the table\. You do not have to run `ALTER TABLE ADD PARTITION` to load the partitions\.

```
CREATE EXTERNAL TABLE `waf_logs`(
  `timestamp` bigint,
  `formatversion` int,
  `webaclid` string,
  `terminatingruleid` string,
  `terminatingruletype` string,
  `action` string,
  `terminatingrulematchdetails` array<
                                  struct<
                                    conditiontype:string,
                                    location:string,
                                    matcheddata:array<string>
                                        >
                                     >,
  `httpsourcename` string,
  `httpsourceid` string,
  `rulegrouplist` array<
                     struct<
                        rulegroupid:string,
                        terminatingrule:struct<
                           ruleid:string,
                           action:string,
                           rulematchdetails:string
                                               >,
                        nonterminatingmatchingrules:array<
                                                       struct<
                                                          ruleid:string,
                                                          action:string,
                                                          rulematchdetails:array<
                                                               struct<
                                                                  conditiontype:string,
                                                                  location:string,
                                                                  matcheddata:array<string>
                                                                     >
                                                                  >
                                                               >
                                                            >,
                        excludedrules:array<
                                         struct<
                                            ruleid:string,
                                            exclusiontype:string
                                               >
                                            >
                           >
                       >,
  `ratebasedrulelist` array<
                        struct<
                          ratebasedruleid:string,
                          limitkey:string,
                          maxrateallowed:int
                              >
                           >,
  `nonterminatingmatchingrules` array<
                                  struct<
                                    ruleid:string,
                                    action:string
                                        >
                                     >,
  `requestheadersinserted` string,
  `responsecodesent` string,
  `httprequest` struct<
                      clientip:string,
                      country:string,
                      headers:array<
                                struct<
                                  name:string,
                                  value:string
                                      >
                                   >,
                      uri:string,
                      args:string,
                      httpversion:string,
                      httpmethod:string,
                      requestid:string
                      >,
  `labels` array<
             struct<
               name:string
                   >
                  >
)
PARTITIONED BY
(
 day STRING
)
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION 's3://bucket/folder/'
TBLPROPERTIES
(
 "projection.enabled" = "true",
 "projection.day.type" = "date",
 "projection.day.range" = "2021/01/01,NOW",
 "projection.day.format" = "yyyy/MM/dd",
 "projection.day.interval" = "1",
 "projection.day.interval.unit" = "DAYS",
 "storage.location.template" = "s3://bucket/folder/${day}"
)
```

For more information about partition projection, see [Partition Projection with Amazon Athena](partition-projection.md)\.

## Example Queries for AWS WAF Logs<a name="query-examples-waf-logs"></a>

In the following example queries, modify the table name, column values, and other variables according to your requirements\. To improve the performance of your queries and reduce cost, add the partition column in the filter condition\.

**Count the number of referers that contain a specified term**  
The following query counts the number of referers that contain the term "amazon" for the specified date range\.

```
WITH test_dataset AS 
  (SELECT header FROM waf_logs
    CROSS JOIN UNNEST(httprequest.headers) AS t(header) WHERE day >= '2021/03/01'
    AND day < '2021/03/31')
SELECT COUNT(*) referer_count 
FROM test_dataset 
WHERE LOWER(header.name)='referer' AND header.value LIKE '%amazon%'
```

**Count all matched IP addresses in the last 10 days that have matched excluded rules**  
 The following query counts the number of times in the last 10 days that the IP address matched the excluded rule in the rule group\. 

```
WITH test_dataset AS 
  (SELECT * FROM waf_logs 
    CROSS JOIN UNNEST(rulegrouplist) AS t(allrulegroups))
SELECT COUNT(*) AS
  count, 
  "httprequest"."clientip", 
  "allrulegroups"."excludedrules",
  "allrulegroups"."ruleGroupId"
FROM test_dataset 
WHERE allrulegroups.excludedrules IS NOT NULL AND from_unixtime(timestamp/1000) > now() - interval '10' day
GROUP BY "httprequest"."clientip", "allrulegroups"."ruleGroupId", "allrulegroups"."excludedrules"
ORDER BY count DESC
```

### Working with Date and Time<a name="query-examples-waf-logs-date-time"></a>

**Return the timestamp field in human\-readable ISO 8601 format**  
The following query uses the `from_unixtime` and `to_iso8601` functions to return the `timestamp` field in human\-readable ISO 8601 format \(for example, `2019-12-13T23:40:12.000Z` instead of `1576280412771`\)\. The query also returns the HTTP source name, source ID, and request\. 

```
SELECT to_iso8601(from_unixtime(timestamp / 1000)) as time_ISO_8601,
       httpsourcename,
       httpsourceid,
       httprequest
FROM waf_logs
LIMIT 10;
```

**Return records from the last 24 hours**  
The following query uses a filter in the `WHERE` clause to return the HTTP source name, HTTP source ID, and HTTP request fields for records from the last 24 hours\.

```
SELECT to_iso8601(from_unixtime(timestamp/1000)) AS time_ISO_8601, 
       httpsourcename, 
       httpsourceid, 
       httprequest 
FROM waf_logs
WHERE from_unixtime(timestamp/1000) > now() - interval '1' day
LIMIT 10;
```

**Return records for a specified date range and IP address**  
The following query lists the records in a specified date range for a specified client IP address\.

```
SELECT * 
FROM waf_logs 
WHERE httprequest.clientip='53.21.198.66' AND day >= '2021/03/01' AND day < '2021/03/31'
```

**For a specified date range, count the number of IP addresses in five minute intervals**  
The following query counts, for a particular date range, the number of IP addresses in five minute intervals\.

```
WITH test_dataset AS 
  (SELECT 
     format_datetime(from_unixtime((timestamp/1000) - ((minute(from_unixtime(timestamp / 1000))%5) * 60)),'yyyy-MM-dd HH:mm') AS five_minutes_ts,
     "httprequest"."clientip" 
     FROM waf_logs 
     WHERE day >= '2021/03/01' AND day < '2021/03/31')
SELECT five_minutes_ts,"clientip",count(*) ip_count 
FROM test_dataset 
GROUP BY five_minutes_ts,"clientip"
```

**Count the number of X\-Forwarded\-For IP in the last 10 days**  
The following query filters the request headers and counts the number of X\-Forwarded\-For IP in the last 10 days\.

```
WITH test_dataset AS
  (SELECT header
   FROM waf_logs
   CROSS JOIN UNNEST (httprequest.headers) AS t(header)
   WHERE from_unixtime("timestamp"/1000) > now() - interval '10' DAY) 
SELECT header.value AS ip,
       count(*) AS COUNT 
FROM test_dataset 
WHERE header.name='X-Forwarded-For' 
GROUP BY header.value 
ORDER BY COUNT DESC
```

For more information about date and time functions, see [Date and Time Functions and Operators](https://prestodb.io/docs/0.217/functions/datetime.html) in the Presto documentation\.

### Working with Blocked Requests and Addresses<a name="query-examples-waf-logs-blocked-requests"></a>

**Extract the top 100 IP addresses blocked by a specified rule type**  
The following query extracts and counts the top 100 IP addresses that have been blocked by the `RATE_BASED` terminating rule during the specified date range\.

```
SELECT COUNT(httpRequest.clientIp) as count,
httpRequest.clientIp
FROM waf_logs
WHERE terminatingruletype='RATE_BASED' AND action='BLOCK' and day >= '2021/03/01'
AND day < '2021/03/31'
GROUP BY httpRequest.clientIp
ORDER BY count DESC
LIMIT 100
```

**Count the number of times a request from a specified country has been blocked**  
The following query counts the number of times the request has arrived from an IP address that belongs to Ireland \(IE\) and has been blocked by the `RATE_BASED` terminating rule\.

```
SELECT COUNT(httpRequest.country) as count,
httpRequest.country
FROM waf_logs
WHERE 
  terminatingruletype='RATE_BASED' AND 
  httpRequest.country='IE'
GROUP BY httpRequest.country
ORDER BY count
LIMIT 100;
```

**Count the number of times a request has been blocked, grouping by specific attributes**  
The following query counts the number of times the request has been blocked, with results grouped by WebACL, RuleId, ClientIP, and HTTP Request URI\.

```
SELECT COUNT(*) AS
  count,
  webaclid,
  terminatingruleid,
  httprequest.clientip,
  httprequest.uri
FROM waf_logs
WHERE action='BLOCK'
GROUP BY webaclid, terminatingruleid, httprequest.clientip, httprequest.uri
ORDER BY count DESC
LIMIT 100;
```

**Count the number of times a specific terminating rule ID has been matched**  
The following query counts the number of times a specific terminating rule ID has been matched \(`WHERE terminatingruleid='e9dd190d-7a43-4c06-bcea-409613d9506e'`\)\. The query then groups the results by WebACL, Action, ClientIP, and HTTP Request URI\.

```
SELECT COUNT(*) AS
  count,
  webaclid,
  action,
  httprequest.clientip,
  httprequest.uri
FROM waf_logs
WHERE terminatingruleid='e9dd190d-7a43-4c06-bcea-409613d9506e'
GROUP BY webaclid, action, httprequest.clientip, httprequest.uri
ORDER BY count DESC
LIMIT 100;
```

**Retrieve the top 100 IP addresses blocked during a specified date range**  
The following query extracts the top 100 IP addresses that have been blocked for a specified date range\. The query also lists the number of times the IP addresses have been blocked\.

```
SELECT "httprequest"."clientip", "count"(*) "ipcount", "httprequest"."country"
FROM waf_logs
WHERE "action" = 'BLOCK' and day >= '2021/03/01'
AND day < '2021/03/31'
GROUP BY "httprequest"."clientip", "httprequest"."country"
ORDER BY "ipcount" DESC limit 100
```
