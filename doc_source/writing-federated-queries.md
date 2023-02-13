# Writing federated queries<a name="writing-federated-queries"></a>

After you have configured one or more data connectors and deployed them to your account, you can use them in your Athena queries\. 

## Querying a single data source<a name="writing-federated-queries-single-data-source"></a>

The examples in this section assume that you have configured and deployed the Athena CloudWatch connector to your account\. Use the same approach to query when you use other connectors\.

**To create an Athena query that uses the CloudWatch connector**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. In the Athena query editor, create a SQL query that uses the following syntax in the `FROM` clause\.

   ```
   MyCloudwatchCatalog.database_name.table_name       
   ```

### Examples<a name="writing-federated-queries-single-data-source-examples"></a>

The following example uses the Athena CloudWatch connector to connect to the `all_log_streams` view in the `/var/ecommerce-engine/order-processor` CloudWatch Logs [Log group](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html)\. The `all_log_streams` view is a view of all the log streams in the log group\. The example query limits the number of rows returned to 100\.

**Example**  

```
SELECT * FROM "MyCloudwatchCatalog"."/var/ecommerce-engine/order-processor".all_log_streams limit 100;
```

The following example parses information from the same view as the previous example\. The example extracts the order ID and log level and filters out any message that has the level `INFO`\.

**Example**  

```
SELECT log_stream as ec2_instance,
                        Regexp_extract(message '.*orderId=(\d+) .*', 1) AS orderId,
                        message                                         AS
                        order_processor_log,
                        Regexp_extract(message, '(.*):.*', 1)           AS log_level
            FROM
                    "MyCloudwatchCatalog"."/var/ecommerce-engine/order-processor".all_log_streams
            WHERE Regexp_extract(message, '(.*):.*', 1) != 'INFO'
```

The following image shows a sample result\.

**Note**  
This example shows a query where the data source has been registered as a catalog with Athena\. You can also reference a data source connector Lambda function using the format `lambda:MyLambdaFunctionName.`

![\[Example Athena query output.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-serverless-app-repo-1.png)

## Querying multiple data sources<a name="writing-federated-queries-multiple-sources"></a>

As a more complex example, imagine an ecommerce company that has an application infrastructure such as the one shown in the following diagram\.

![\[Example ecommerce infrastructure with a variety of data sources.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-serverless-app-repo-2.png)

The following descriptions explain the numbered items in the diagram\.

1. Payment processing in a secure VPC with transaction records stored in HBase on Amazon EMR

1. Redis to store active orders so that the processing engine can access them quickly

1. Amazon DocumentDB for customer account data such as email addresses and shipping addresses

1. A product catalog in Amazon Aurora for an ecommerce site that uses automatic scaling on Fargate

1. CloudWatch Logs to house the order processor's log events

1. A write\-once\-read\-many data warehouse on Amazon Redshift

1. DynamoDB to store shipment tracking data

Imagine that a data analyst for this ecommerce application discovers that the state of some orders is being reported erroneously\. Some orders show as pending even though they were delivered, while others show as delivered but haven't shipped\.

The analyst wants to know how many orders are being delayed and what the affected orders have in common across the ecommerce infrastructure\. Instead of investigating the sources of information separately, the analyst federates the data sources and retrieves the necessary information in a single query\. Extracting the data into a single location is not necessary\.

The analyst's query uses the following Athena data connectors:
+ [CloudWatch Logs](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-cloudwatch) – Retrieves logs from the order processing service and uses regex matching and extraction to filter for orders with `WARN` or `ERROR` events\.
+ [Redis](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-redis) – Retrieves the active orders from the Redis instance\.
+ [CMDB](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-aws-cmdb) – Retrieves the ID and state of the Amazon EC2 instance that ran the order processing service and logged the `WARN` or `ERROR` message\.
+ [DocumentDB](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-docdb) – Retrieves the customer email and address from Amazon DocumentDB for the affected orders\.
+ [DynamoDB](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-dynamodb) – Retrieves the shipping status and tracking details from the shipping table to identify possible discrepancies between reported and actual status\.
+ [HBase](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-hbase) – Retrieves the payment status for the affected orders from the payment processing service\.

**Example**  
This example shows a query where the data source has been registered as a catalog with Athena\. You can also reference a data source connector Lambda function using the format `lambda:MyLambdaFunctionName.`

```
--Sample query using multiple Athena data connectors.
WITH logs 
     AS (SELECT log_stream, 
                message                                          AS 
                order_processor_log, 
                Regexp_extract(message, '.*orderId=(\d+) .*', 1) AS orderId, 
                Regexp_extract(message, '(.*):.*', 1)            AS log_level 
         FROM 
     "MyCloudwatchCatalog"."/var/ecommerce-engine/order-processor".all_log_streams 
         WHERE  Regexp_extract(message, '(.*):.*', 1) != 'INFO'), 
     active_orders 
     AS (SELECT * 
         FROM   redis.redis_db.redis_customer_orders), 
     order_processors 
     AS (SELECT instanceid, 
                publicipaddress, 
                state.NAME 
         FROM   awscmdb.ec2.ec2_instances), 
     customer 
     AS (SELECT id, 
                email 
         FROM   docdb.customers.customer_info), 
     addresses 
     AS (SELECT id, 
                is_residential, 
                address.street AS street 
         FROM   docdb.customers.customer_addresses),
     shipments 
     AS ( SELECT order_id, 
                 shipment_id, 
                 from_unixtime(cast(shipped_date as double)) as shipment_time,
                 carrier
        FROM lambda_ddb.default.order_shipments),
     payments
     AS ( SELECT "summary:order_id", 
                 "summary:status", 
                 "summary:cc_id", 
                 "details:network" 
        FROM "hbase".hbase_payments.transactions)
         
SELECT _key_            AS redis_order_id, 
       customer_id, 
       customer.email   AS cust_email, 
       "summary:cc_id"  AS credit_card,
       "details:network" AS CC_type,
       "summary:status" AS payment_status,
       status           AS redis_status, 
       addresses.street AS street_address, 
       shipments.shipment_time as shipment_time,
       shipments.carrier as shipment_carrier,
       publicipaddress  AS ec2_order_processor, 
       NAME             AS ec2_state, 
       log_level, 
       order_processor_log 
FROM   active_orders 
       LEFT JOIN logs 
              ON logs.orderid = active_orders._key_ 
       LEFT JOIN order_processors 
              ON logs.log_stream = order_processors.instanceid 
       LEFT JOIN customer 
              ON customer.id = customer_id 
       LEFT JOIN addresses 
              ON addresses.id = address_id 
       LEFT JOIN shipments
              ON shipments.order_id = active_orders._key_
       LEFT JOIN payments
              ON payments."summary:order_id" = active_orders._key_
```

The following image shows sample results of the query\.

![\[Sample results from a federated query in Athena.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-serverless-app-repo-3.png)