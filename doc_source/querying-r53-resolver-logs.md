# Querying Amazon Route 53 Resolver Query Logs<a name="querying-r53-resolver-logs"></a>

You can create Athena tables for your Amazon Route 53 Resolver query logs and query them from Athena\.

Route 53 Resolver query logging is for logging of DNS queries made by resources within a VPC, on\-premises resources that use inbound resolver endpoints, queries that use an outbound Resolver endpoint for recursive DNS resolution, and queries that use Route 53 Resolver DNS firewall rules to block, allow, or monitor a domain list\. For more information about Resolver query logging, see [Resolver query logging](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs.html) in the *Amazon Route 53 Developer Guide*\. For information about each of the fields in the logs, see [Values that appear in Resolver query logs](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs-format.html) in the *Amazon Route 53 Developer Guide*\.

## Creating the Table for Resolver Query Logs<a name="querying-r53-resolver-logs-creating-the-table"></a>

You can use the Query Editor in the Athena console to create and query a table for your Route 53 Resolver query logs\.

**To create and query an Athena table for Route 53 Resolver query logs**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. In the Athena Query Editor, enter the following `CREATE TABLE` statement\. Replace the `LOCATION` clause values with those corresponding to the location of your Resolver logs in Amazon S3\.

   ```
   CREATE EXTERNAL TABLE r53_rlogs (
     version string,
     account_id string,
     region string,
     vpc_id string,
     query_timestamp string,
     query_name string,
     query_type string,
     query_class
       string,
     rcode string,
     answers array<
       struct<
         Rdata: string,
         Type: string,
         Class: string>
       >,
     srcaddr string,
     srcport int,
     transport string,
     srcids struct<
       instance: string,
       resolver_endpoint: string
       >
    )
        
   ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
   LOCATION 's3://your_log_bucket/AWSLogs/{account_id}/vpcdnsquerylogs/{vpc-id}/YYYY/MM/dd/'
   ```

   Because Resolver query log data is in JSON format, the CREATE TABLE statement uses a [JSON SerDe library](json-serde.md) to analyze the data\.
**Note**  
The SerDe expects each JSON record in the logs in Amazon S3 to be on a single line of text with no line termination characters separating the fields in the record\. If the log JSON text is in pretty print format, you may receive the error message HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object when you attempt to query the table after you create it\.

1. Choose **Run query**\. The statement creates an Athena table named `r53_rlogs` whose columns represent each of the fields in your Resolver log data\.

1. In the Athena console Query Editor, run the following query to verify that your table has been created\.

   ```
   SELECT * FROM "r53_rlogs" LIMIT 10
   ```

## Example Queries<a name="querying-r53-resolver-logs-example-queries"></a>

The following examples show some queries that you can perform from Athena on your Resolver query logs\.

### Example 1 \- Query logs in descending query\_timestamp order<a name="querying-r53-resolver-logs-example-1-query-logs-in-descending-query_timestamp-order"></a>

The following query displays log results in descending `query_timestamp` order\.

```
SELECT * FROM "r53_rlogs"
ORDER BY query_timestamp DESC
```

### Example 2 \- Query logs within specified start and end times<a name="querying-r53-resolver-logs-example-2-query-logs-within-specified-start-and-end-times"></a>

The following query queries logs between midnight and 8am on September 24, 2020\. Substitute the start and end times according to your own requirements\.

```
SELECT query_timestamp, srcids.instance, srcaddr, srcport, query_name, rcode
FROM "r53_rlogs"
WHERE (parse_datetime(query_timestamp,'yyyy-MM-dd''T''HH:mm:ss''Z')
     BETWEEN parse_datetime('2020-09-24-00:00:00','yyyy-MM-dd-HH:mm:ss') 
     AND parse_datetime('2020-09-24-00:08:00','yyyy-MM-dd-HH:mm:ss'))
ORDER BY query_timestamp DESC
```

### Example 3 \- Query logs based on a specified DNS query name pattern<a name="querying-r53-resolver-logs-example-3-query-logs-based-on-a-specified-dns-query-name-pattern"></a>

The following query selects records whose query name includes the string "example\.com"\.

```
SELECT query_timestamp, srcids.instance, srcaddr, srcport, query_name, rcode, answers
FROM "r53_rlogs"
WHERE query_name LIKE '%example.com%'
ORDER BY query_timestamp DESC
```

### Example 4 \- Query log requests with no answer<a name="querying-r53-resolver-logs-example-4-query-log-requests-with-no-answer"></a>

The following query selects log entries in which the request received no answer\.

```
SELECT query_timestamp, srcids.instance, srcaddr, srcport, query_name, rcode, answers
FROM "r53_rlogs"
WHERE cardinality(answers) = 0
```

### Example 5 \- Query logs with a specific answer<a name="querying-r53-resolver-logs-example-5-query-logs-with-a-specific-answer"></a>

The following query shows logs in which the `answer.Rdata` value has the specified IP address\.

```
SELECT query_timestamp, srcids.instance, srcaddr, srcport, query_name, rcode, answer.Rdata
FROM "r53_rlogs"
CROSS JOIN UNNEST(r53_rlogs.answers) as st(answer)
WHERE answer.Rdata='203.0.113.16';
```
