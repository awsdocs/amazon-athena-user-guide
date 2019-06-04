# Querying AWS WAF Logs<a name="waf-logs"></a>

AWS WAF logs include information about the traffic that is analyzed by your web ACL, such as the time that AWS WAF received the request from your AWS resource, detailed information about the request, and the action for the rule that each request matched\.

You can enable access logging for AWS WAF logs, save them to Amazon S3, and query the logs in Athena\. For more information about enabling AWS WAF logs and about the log record structure, see [Logging Web ACL Traffic Information](https://docs.aws.amazon.com/waf/latest/developerguide/logging.html) in the *AWS WAF Developer Guide*\.

Make a note of the Amazon S3 bucket to which you save these logs\.
+  [Creating the Table for AWS WAF Logs](#create-waf-table) 
+  [Example Queries for AWS WAF logs](#query-examples-waf-logs) 

## Creating the Table for AWS WAF Logs<a name="create-waf-table"></a>

### To create the AWS WAF table<a name="to-create-the-waf-table"></a>

1. Copy and paste the following DDL statement into the Athena console\. Modify the `LOCATION` for the Amazon S3 bucket that stores your logs\.

   This query uses the [Hive JSON SerDe](json.md#hivejson)\. The table format and the SerDe are suggested by the AWS Glue crawler when it analyzes AWS WAF logs\.

   ```
   CREATE EXTERNAL TABLE `waf_logs`(
     `timestamp` bigint,
     `formatversion` int,
     `webaclid` string,
     `terminatingruleid` string,
     `terminatingruletype` string,
     `action` string,
     `httpsourcename` string,
     `httpsourceid` string,
     `rulegrouplist` array<string>,
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
                         > 
   )
   ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
   WITH SERDEPROPERTIES (
    'paths'='action,formatVersion,httpRequest,httpSourceId,httpSourceName,nonTerminatingMatchingRules,rateBasedRuleList,ruleGroupList,terminatingRuleId,terminatingRuleType,timestamp,webaclId')
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