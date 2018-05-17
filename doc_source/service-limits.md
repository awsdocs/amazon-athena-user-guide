# Service Limits<a name="service-limits"></a>

**Note**  
You can contact AWS Support to [request a limit increase](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) for the limits listed here\.
+ By default, concurrency limits on your account allow you to submit twenty concurrent DDL queries \(used for creating tables and adding partitions\) and twenty concurrent SELECT queries at a time\. This is a soft limit and you can [request a limit increase](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) for concurrent queries\.

  Concurrency limits in Athena are defined as the number of queries that can be submitted to the service concurrently\. You can submit up to twenty queries of the same type \(`DDL` or `SELECT`\) at a time\. If you submit a query that exceeds the concurrent query limit, the Athena API displays an error message: "You have exceeded the limit for the number of queries you can run concurrently\. Reduce the number of concurrent queries submitted by this account\. Contact customer support to request a concurrent query limit increase\.” 

  After you submit your queries to Athena, it processes the queries by assigning resources based on the overall service load and the amount of incoming requests\. We continuously monitor and make adjustments to the service so that your queries process as fast as possible\. 
+ If you use Athena in regions where AWS Glue is available, migrate to AWS Glue Catalog\. See [Upgrading to the AWS Glue Data Catalog Step\-by\-Step](glue-upgrade.md)\. If you have migrated to AWS Glue, for service limits on tables, databases, and partitions in Athena, see [AWS Glue Limits](http://docs.aws.amazon.com/glue/latest/dg/troubleshooting-service-limits.html)\.
+ If you have not migrated to AWS Glue Catalog, you can [request a limit increase](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.
+ You may encounter a limit for Amazon S3 buckets per account, which is 100\. Athena also needs a separate bucket to log results\.
+ Query timeout: 30 minutes\.