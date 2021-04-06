# Querying AWS WAF Logs<a name="waf-logs"></a>

AWS WAF logs include information about the traffic that is analyzed by your web ACL, such as the time that AWS WAF received the request from your AWS resource, detailed information about the request, and the action for the rule that each request matched\.

You can enable access logging for AWS WAF logs and save them to Amazon S3\. Make a note of the Amazon S3 bucket to which you save these logs, and you can create an Athena table for them and query them in Athena\. 

For more information about enabling AWS WAF logs and about the log record structure, see [Logging web ACL traffic information](https://docs.aws.amazon.com/waf/latest/developerguide/logging.html) in the *AWS WAF Developer Guide*\.

For an example of how to aggregate AWS WAF logs into a central data lake repository and query them with Athena, see the AWS Big Data Blog post [Analyzing AWS WAF logs with Amazon ES, Amazon Athena, and Amazon QuickSight](http://aws.amazon.com/blogs/big-data/analyzing-aws-waf-logs-with-amazon-es-amazon-athena-and-amazon-quicksight/)\.

**Topics**
+ [Creating the Table for AWS WAF Logs](#create-waf-table)
+ [Example Queries for AWS WAF Logs](#query-examples-waf-logs)

## Creating the Table for AWS WAF Logs<a name="create-waf-table"></a>

### To create the AWS WAF table<a name="to-create-the-waf-table"></a>

1. Copy and paste the following DDL statement into the Athena console\. Modify the `LOCATION` for the Amazon S3 bucket that stores your logs\.

   This query uses the [OpenX JSON SerDe](json-serde.md#openx-json-serde)\. The table format and the SerDe are suggested by the AWS Glue crawler when it analyzes AWS WAF logs\.
**Note**  
The SerDe expects each JSON record in the WAF logs in Amazon S3 to be on a single line of text with no line termination characters separating the fields in the record\. If the WAF log JSON text is in pretty print format, you may receive the error message HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object when you attempt to query the table after you create it\.

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
                           excludedrules:string
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
    'paths'='action,formatVersion,httpRequest,httpSourceId,httpSourceName,nonTerminatingMatchingRules,rateBasedRuleList,ruleGroupList,terminatingRuleId,terminatingRuleMatchDetails,terminatingRuleType,timestamp,webaclId,labels')
   STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'
   OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION 's3://athenawaflogs/WebACL/'
   ```

1. Run the query in the Athena console\. After the query completes, Athena registers the `waf_logs` table, making the data in it available for queries\.

## Example Queries for AWS WAF Logs<a name="query-examples-waf-logs"></a>

The following query counts the number of times an IP address has been blocked by the `RATE_BASED` terminating rule\.

```
SELECT COUNT(httpRequest.clientIp) as count,
httpRequest.clientIp
FROM waf_logs
WHERE terminatingruletype='RATE_BASED' AND action='BLOCK'
GROUP BY httpRequest.clientIp
ORDER BY count 
LIMIT 100;
```

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

The following query uses the `from_unixtime` and `to_iso8601` functions to return the `timestamp` field in human\-readable ISO 8601 format \(for example, `2019-12-13T23:40:12.000Z` instead of `1576280412771`\)\. The query also returns the HTTP source name, source ID, and request\. 

```
SELECT to_iso8601(from_unixtime(timestamp / 1000)) as time_ISO_8601,
       httpsourcename,
       httpsourceid,
       httprequest
FROM waf_logs
LIMIT 10;
```

The following query uses a filter in the `WHERE` clause to return the same fields for records from the last 24 hours\.

```
SELECT to_iso8601(from_unixtime(timestamp/1000)) AS time_ISO_8601, 
       httpsourcename, 
       httpsourceid, 
       httprequest 
FROM waf_logs
WHERE from_unixtime(timestamp/1000) > now() - interval '1' day
LIMIT 10;
```

For more information about date and time functions, see [Date and Time Functions and Operators](https://prestodb.io/docs/0.217/functions/datetime.html) in the Presto documentation\.