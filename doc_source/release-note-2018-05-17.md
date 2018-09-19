# May 17, 2018<a name="release-note-2018-05-17"></a>

Published on *2018\-05\-17*

Increased query concurrency limits in Athena from five to twenty\. This means that you can submit and run up to twenty `DDL` queries and twenty `SELECT` queries at a time\. Note that the concurrency limits are separate for `DDL` and `SELECT` queries\. 

Concurrency limits in Athena are defined as the number of queries that can be submitted to the service concurrently\. You can submit up to twenty queries of the same type \(`DDL` or `SELECT`\) at a time\. If you submit a query that exceeds the concurrent query limit, the Athena API displays an error message: "You have exceeded the limit for the number of queries you can run concurrently\. Reduce the number of concurrent queries submitted by this account\. Contact customer support to request a concurrent query limit increase\.” 

After you submit your queries to Athena, it processes the queries by assigning resources based on the overall service load and the amount of incoming requests\. We continuously monitor and make adjustments to the service so that your queries process as fast as possible\. 

For information, see [Service Limits](service-limits.md)\. This is a soft limit and you can [request a limit increase](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) for concurrent queries\.