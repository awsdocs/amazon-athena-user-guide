# Service Limits<a name="service-limits"></a>

**Note**  
You can contact AWS Support to [request a limit increase](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) for the limits listed here\.
+ By default, concurrency limits on your account allow you to submit five concurrent DDL queries \(used for creating tables and adding partitions\) and five concurrent SELECT queries at a time\. This is a soft limit and you can [request a limit increase](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) for concurrent queries\.
+ If you use Athena in regions where AWS Glue is available, migrate to AWS Glue Catalog\. See [Upgrading to the AWS Glue Data Catalog Step\-by\-Step](glue-upgrade.md)\. If you have migrated to AWS Glue, for service limits on tables, databases, and partitions in Athena, see [AWS Glue Limits](http://docs.aws.amazon.com/glue/latest/dg/troubleshooting-service-limits.html)\.
+ If you have not migrated to AWS Glue Catalog, you can [request a limit increase](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.
+ You may encounter a limit for Amazon S3 buckets per account, which is 100\. Athena also needs a separate bucket to log results\.
+ Query timeout: 30 minutes