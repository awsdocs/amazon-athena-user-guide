# Service Limits<a name="service-limits"></a>

**Note**  
You can contact AWS Support to [request a limit increase](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) for the limits listed here\.
+ By default, limits on your account allow you to submit: 
  + 20 DDL queries at the same time\. DDL queries include `CREATE TABLE` and `CREATE TABLE ADD PARTITION` queries\. 
  + 20 DML queries at the same time\. DML queries include `SELECT` and `CREATE TABLE AS` \(CTAS\) queries\)\.

  After you submit your queries to Athena, it processes the queries by assigning resources based on the overall service load and the amount of incoming requests\. We continuously monitor and make adjustments to the service so that your queries process as fast as possible\. 

   These are soft limits and you can [request a limit increase](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\. These limits in Athena are defined as the number of queries that can be submitted to the service at the same time\. You can submit up to 20 queries of the same type \(DDL or DML\) at a time\. If you submit a query that exceeds the query limit, the Athena API displays an error message: "You have exceeded the limit for the number of queries you can run concurrently\. Reduce the number of concurrent queries submitted by this account\. Contact customer support to request a concurrent query limit increase\.” 
+ If you use Athena in regions where AWS Glue is available, migrate to AWS Glue Data Catalog\. See [Upgrading to the AWS Glue Data Catalog Step\-by\-Step](glue-upgrade.md)\. If you have migrated to AWS Glue, for service limits on tables, databases, and partitions in Athena, see [AWS Glue Limits](https://docs.aws.amazon.com/glue/latest/dg/troubleshooting-service-limits.html)\. If you have not migrated to AWS Glue Catalog, the number of partitions per table is 20,000\. You can [request a limit increase](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.
+ You may encounter a limit for Amazon S3 buckets per account, which is 100\. Athena also needs a separate bucket to log results\.
+ The query timeout is 30 minutes\.
+ The maximum allowed query string length is 262144 bytes\.
+  Athena APIs have the following default limits for the number of calls to the API per account \(not per query\):    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/service-limits.html)

  For example, for `StartQueryExecution`, or any of the other APIs that have the same limits in the previous table, you can make up to 5 calls per second\. In addition, if this API is not called for 4 seconds, your account accumulates a *burst capacity* of up to 20 calls\. In this case, your application can make up to 20 calls to this API in burst mode\.

  If you use any of these APIs and exceed the default limit for the number of calls per second, or the burst capacity in your account, the Athena API issues an error similar to the following: ""ClientError: An error occurred \(ThrottlingException\) when calling the *<API\_name>* operation: Rate exceeded\." Reduce the number of calls per second, or the burst capacity for the API for this account\. You can contact AWS Support to [request a limit increase](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.