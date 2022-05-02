# Service Quotas<a name="service-limits"></a>

**Note**  
The Service Quotas console provides information about Amazon Athena quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/athena/quotas) for the quotas that are adjustable\.

## Queries<a name="service-limits-queries"></a>

Your account has the following query\-related quotas for Amazon Athena\. For details, see the [Amazon Athena endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/athena.html#amazon-athena-limits) page of the AWS General Reference\.
+ **Active DDL queries** – The number of active DDL queries\. DDL queries include `CREATE TABLE` and `ALTER TABLE ADD PARTITION` queries\. 
+ **DDL query timeout** – The maximum amount of time in minutes a DDL query can run before it gets cancelled\.
+ **Active DML queries** – The number of active DML queries\. DML queries include `SELECT`, `CREATE TABLE AS` \(CTAS\), and `INSERT INTO` queries\. The specific quotas vary by AWS Region\.
+ **DML query timeout** – The maximum amount of time in minutes a DML query can run before it gets cancelled\.

These are soft quotas; you can use the [Athena Service Quotas](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/athena/quotas) console to request a quota increase\.

Athena processes queries by assigning resources based on the overall service load and the number of incoming requests\. Your queries may be temporarily queued before they run\. Asynchronous processes pick up the queries from queues and run them on physical resources as soon as the resources become available and for as long as your account configuration permits\.

A DML or DDL query quota includes both running and queued queries\. For example, if your DML query quota is 25 and your total of running and queued queries is 26, query 26 will result in a TooManyRequestsException error\. 

### Query string length<a name="service-limits-query-string-length"></a>

The maximum allowed query string length is 262144 bytes, where the strings are encoded in UTF\-8\. This is not an adjustable quota\. However, you can work around this limitation by splitting long queries into multiple smaller queries\. For more information, see [How can I increase the maximum query string length in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-query-string-length/) in the AWS Knowledge Center\.

**Note**  
If you require a greater query string length, provide feedback at [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com) with the details of your use case, or contact [AWS Support](https://console.aws.amazon.com/support/home/)\.

## Workgroups<a name="service-limits-workgroups"></a>

When you work with Athena workgroups, remember the following points:
+ Athena service quotas are shared across all workgroups in an account\.
+ The maximum number of workgroups you can create per Region in an account is 1000\.
+ The maximum number of tags per workgroup is 50\. For more information, see [Tag restrictions](tags.md#tag-restrictions)\. 

## Databases, tables, and partitions<a name="service-limits-glue"></a>
+ If you are using the AWS Glue Data Catalog with Athena, see [AWS Glue endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/glue.html) for service quotas on tables, databases, and partitions – for example, the maximum number of databases or tables per account\. 
  + Although Athena supports querying AWS Glue tables that have 10 million partitions, Athena cannot read more than 1 million partitions in a single scan\.
+ If you are not using AWS Glue Data Catalog, the number of partitions per table is 20,000\. You can [request a quota increase](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/glue/quotas)\.

**Note**  
If you have not yet migrated to AWS Glue Data Catalog, see [Upgrading to the AWS Glue Data Catalog Step\-by\-Step](glue-upgrade.md) for migration instructions\.

## Amazon S3 buckets<a name="service-limits-buckets"></a>

When you work with Amazon S3 buckets, remember the following points:
+ Amazon S3 has a default service quota of 100 buckets per account\.
+ Athena requires a separate bucket to log results\.
+ You can request a quota increase of up to 1,000 Amazon S3 buckets per AWS account\. 

## Per account API call quotas<a name="service-limits-api-calls"></a>

Athena APIs have the following default quotas for the number of calls to the API per account \(not per query\):


| API name | Default number of calls per second | Burst capacity | 
| --- | --- | --- | 
| BatchGetNamedQuery, ListNamedQueries, ListQueryExecutions  | 5 | up to 10 | 
| CreateNamedQuery, DeleteNamedQuery, GetNamedQuery | 5 | up to 20 | 
| BatchGetQueryExecution | 20 | up to 40 | 
| StartQueryExecution, StopQueryExecution | 20 | up to 80 | 
| GetQueryExecution, GetQueryResults | 100 | up to 200 | 

For example, you can make up to 20 calls per second for `StartQueryExecution`\. In addition, if this API is not called for 4 seconds, your account accumulates a *burst capacity* of up to 80 calls\. In this case, your application can make up to 80 calls to this API in burst mode\.

If you use any of these APIs and exceed the default quota for the number of calls per second, or the burst capacity in your account, the Athena API issues an error similar to the following: ""ClientError: An error occurred \(ThrottlingException\) when calling the *<API\_name>* operation: Rate exceeded\." Reduce the number of calls per second, or the burst capacity for the API for this account\. To request a quota increase, contact AWS Support\. Open the [AWS Support center](https://console.aws.amazon.com/support/home#/) page, sign in if necessary, and choose **Create case**\. Choose **Service limit increase**\. Complete and submit the form\.

**Note**  
This quota cannot be changed in the Athena Service Quotas console\.