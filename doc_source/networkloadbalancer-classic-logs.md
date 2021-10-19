# Querying Network Load Balancer Logs<a name="networkloadbalancer-classic-logs"></a>

Use Athena to analyze and process logs from Network Load Balancer\. These logs receive detailed information about the Transport Layer Security \(TLS\) requests sent to the Network Load Balancer\. You can use these access logs to analyze traffic patterns and troubleshoot issues\. 

Before you analyze the Network Load Balancer access logs, enable and configure them for saving in the destination Amazon S3 bucket\. For more information, see [ Access Logs for Your Network Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-access-logs.html)\.
+ [Create the table for Network Load Balancer logs](#to-create-the-network-logs-table)
+ [Network Load Balancer Example Queries](#query-nlb-example)

## To create the table for Network Load Balancer logs<a name="to-create-the-network-logs-table"></a>

1. Copy and paste the following DDL statement into the Athena console\. Check the [syntax ](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-access-logs.html#access-log-file-format) of the Network Load Balancer log records\. You may need to update the following query to include the columns and the Regex syntax for latest version of the record\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS nlb_tls_logs (
               type string,
               version string,
               time string,
               elb string,
               listener_id string,
               client_ip string,
               client_port int,
               target_ip string,
               target_port int,
               tcp_connection_time_ms double,
               tls_handshake_time_ms double,
               received_bytes bigint,
               sent_bytes bigint,
               incoming_tls_alert int,
               cert_arn string,
               certificate_serial string,
               tls_cipher_suite string,
               tls_protocol_version string,
               tls_named_group string,
               domain_name string,
               alpn_fe_protocol string,
               alpn_be_protocol string,
               alpn_client_preference_list string
               )
               ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
               WITH SERDEPROPERTIES (
               'serialization.format' = '1',
               'input.regex' = 
           '([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*):([0-9]*) ([-.0-9]*) ([-.0-9]*) ([-0-9]*) ([-0-9]*) ([-0-9]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*)$')
               LOCATION 's3://your_log_bucket/prefix/AWSLogs/AWS_account_ID/elasticloadbalancing/region';
   ```

1. Modify the `LOCATION` Amazon S3 bucket to specify the destination of your Network Load Balancer logs\.

1. Run the query in the Athena console\. After the query completes, Athena registers the `nlb_tls_logs` table, making the data in it ready for queries\.

## Network Load Balancer Example Queries<a name="query-nlb-example"></a>

To see how many times a certificate is used, use a query similar to this example:

```
SELECT count(*) AS 
         ct,
         cert_arn
FROM "nlb_tls_logs"
GROUP BY  cert_arn;
```

The following query shows how many users are using a TLS version earlier than 1\.3:

```
SELECT tls_protocol_version,
         COUNT(tls_protocol_version) AS 
         num_connections,
         client_ip
FROM "nlb_tls_logs"
WHERE tls_protocol_version < 'tlsv13'
GROUP BY tls_protocol_version, client_ip;
```

Use the following query to identify connections that take a long TLS handshake time:

```
SELECT *
FROM "nlb_tls_logs"
ORDER BY  tls_handshake_time_ms DESC 
LIMIT 10;
```

Use the following query to identify and count which TLS protocol versions and cipher suites have been negotiated in the past 30 days\.

```
SELECT tls_cipher_suite,
         tls_protocol_version,
         COUNT(*) AS ct
FROM "nlb_tls_logs"
WHERE from_iso8601_timestamp(time) > current_timestamp - interval '30' day
        AND NOT tls_protocol_version = '-'
GROUP BY tls_cipher_suite, tls_protocol_version
ORDER BY ct DESC;
```