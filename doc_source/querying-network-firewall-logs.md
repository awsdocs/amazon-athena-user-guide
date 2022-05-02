# Querying AWS Network Firewall logs<a name="querying-network-firewall-logs"></a>

AWS Network Firewall is a managed service that you can use to deploy essential network protections for your Amazon Virtual Private Cloud instances\. AWS Network Firewall works together with AWS Firewall Manager so you can build policies based on AWS Network Firewall rules and then centrally apply those policies across your VPCs and accounts\. For more information about AWS Network Firewall, see [AWS Network Firewall](http://aws.amazon.com/network-firewall/)\.

You can configure AWS Network Firewall logging for traffic that you forward to your firewall's stateful rules engine\. Logging gives you detailed information about network traffic, including the time that the stateful engine received a packet, detailed information about the packet, and any stateful rule action taken against the packet\. The logs are published to the log destination that you've configured, where you can retrieve and view them\. For more information, see [Logging network traffic from AWS Network Firewall](https://docs.aws.amazon.com/network-firewall/latest/developerguide/firewall-logging.html) in the *AWS Network Firewall Developer Guide*\.

## To create the table for Network Firewall logs<a name="to-create-the-network-firewall-logs-table"></a>

1. Copy and paste the following DDL statement into the Athena Query Editor\. You may need to update the statement to include the columns for the latest version of the logs\. For more information, see [Contents of a firewall log](https://docs.aws.amazon.com/network-firewall/latest/developerguide/firewall-logging.html#firewall-logging-contents) in the *AWS Network Firewall Developer Guide*\.
**Note**  
 Because `END` is a [reserved word](reserved-words.md) in Athena, the statement uses `finish:string` instead of `end:string`\.

   ```
   CREATE EXTERNAL TABLE anf_logs(
     firewall_name string,
     availability_zone string,
     event_timestamp bigint,
     event struct<
       timestamp:string,
       flow_id:bigint,
       event_type:string,
       src_ip:string,
       src_port:int,
       dest_ip:string,
       dest_port:int,
       proto:string,
       app_proto:string,
       netflow:struct<
         pkts:int,
         bytes:int,
         start:string,
         finish:string,
         age:int,
         min_ttl:int,
         max_ttl:int
       >
     >
   )
   ROW FORMAT SERDE
     'org.openx.data.jsonserde.JsonSerDe'
   WITH SERDEPROPERTIES (
     'paths'='availability_zone,event,event_timestamp,firewall_name'
   )
   STORED AS INPUTFORMAT
     'org.apache.hadoop.mapred.TextInputFormat'
   OUTPUTFORMAT
     'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION
     's3://bucket_name/AWSLogs/'
   ```

1. Modify the `LOCATION` Amazon S3 bucket to specify the destination of your Network Firewall logs\.

1. Run the query in the Athena console\. After the query completes, Athena registers the `anf_logs` table, making the data in it ready for queries\.

## Network Firewall example query<a name="querying-network-firewall-logs-example"></a>

The following query shows the count of requests evaluated by AWS Network Firewall that have unique source and destination IP pairs\.

```
SELECT COUNT(*) AS count,
       event.src_ip,
       event.src_port,
       event.dest_ip,
       event.dest_port
FROM anf_logs
GROUP BY event.src_ip,
         event.src_port,
         event.dest_ip,
         event.dest_port
ORDER BY COUNT DESC
LIMIT 100
```