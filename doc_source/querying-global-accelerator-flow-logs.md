# Querying AWS Global Accelerator flow logs<a name="querying-global-accelerator-flow-logs"></a>

You can use AWS Global Accelerator to create accelerators that direct network traffic to optimal endpoints over the AWS global network\. For more information about Global Accelerator, see [What is AWS Global Accelerator](https://docs.aws.amazon.com/global-accelerator/latest/dg/what-is-global-accelerator.html)\.

Global Accelerator flow logs enable you to capture information about the IP address traffic going to and from network interfaces in your accelerators\. Flow log data is published to Amazon S3, where you can retrieve and view your data\. For more information, see [Flow logs in AWS Global Accelerator](https://docs.aws.amazon.com/global-accelerator/latest/dg/monitoring-global-accelerator.flow-logs.html)\.

You can use Athena to query your Global Accelerator flow logs by creating a table that specifies their location in Amazon S3\.

**To create the table for Global Accelerator flow logs**

1. Copy and paste the following DDL statement into the Athena console\. This query specifies *ROW FORMAT DELIMITED* and omits specifying a [SerDe](serde-reference.md), which means that the query uses the [`LazySimpleSerDe`](lazy-simple-serde.md)\. In this query, fields are terminated by a space\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS aga_flow_logs (
     version string,
     account string,
     acceleratorid string,
     clientip string,
     clientport int,
     gip string,
     gipport int,
     endpointip string,
     endpointport int,
     protocol string,
     ipaddresstype string,
     numpackets bigint,
     numbytes int,
     starttime int,
     endtime int,
     action string,
     logstatus string,
     agasourceip string,
     agasourceport int,
     endpointregion string,
     agaregion string,
     direction string
   )
   PARTITIONED BY (dt string)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ' '
   LOCATION 's3://your_log_bucket/prefix/AWSLogs/account_id/globalaccelerator/region/'
   TBLPROPERTIES ("skip.header.line.count"="1");
   ```

1. Modify the `LOCATION` value to point to the Amazon S3 bucket that contains your log data\.

   ```
   's3://your_log_bucket/prefix/AWSLogs/account_id/globalaccelerator/region_code/'
   ```

1. Run the query in the Athena console\. After the query completes, Athena registers the `aga_flow_logs` table, making the data in it available for queries\.

1. Create partitions to read the data, as in the following sample query\. The query creates a single partition for a specified date\. Replace the placeholders for date and location\.

   ```
   ALTER TABLE aga_flow_logs
   ADD PARTITION (dt='YYYY-MM-dd')
   LOCATION 's3://your_log_bucket/prefix/AWSLogs/account_id/globalaccelerator/region_code/YYYY/MM/dd';
   ```

## Example queries for AWS Global Accelerator flow logs<a name="querying-global-accelerator-flow-logs-examples"></a>

**Example – List the requests that pass through a specific edge location**  
The following example query lists requests that passed through the LHR edge location\. Use the `LIMIT` operator to limit the number of logs to query at one time\.  

```
SELECT 
  clientip, 
  agaregion, 
  protocol, 
  action 
FROM 
  aga_flow_logs 
WHERE 
  agaregion LIKE 'LHR%' 
LIMIT 
  100;
```

**Example – List the endpoint IP addresses that receive the most HTTPS requests**  
To see which endpoint IP addresses are receiving the highest number of HTTPS requests, use the following query\. This query counts the number of packets received on HTTPS port 443, groups them by destination IP address, and returns the top 10 IP addresses\.  

```
SELECT 
  SUM(numpackets) AS packetcount, 
  endpointip 
FROM 
  aga_flow_logs 
WHERE 
  endpointport = 443 
GROUP BY 
  endpointip 
ORDER BY 
  packetcount DESC 
LIMIT 
  10;
```