# May 17, 2018<a name="release-note-2018-05-17"></a>

Published on *2018\-05\-17*

Increased query concurrency quota in Athena from five to twenty\. This means that you can submit and run up to twenty `DDL` queries and twenty `SELECT` queries at a time\. Note that the concurrency quotas are separate for `DDL` and `SELECT` queries\. 

Concurrency quotas in Athena are defined as the number of queries that can be submitted to the service concurrently\. You can submit up to twenty queries of the same type \(`DDL` or `SELECT`\) at a time\. If you submit a query that exceeds the concurrent query quota, the Athena API displays an error message\.

After you submit your queries to Athena, it processes the queries by assigning resources based on the overall service load and the amount of incoming requests\. We continuously monitor and make adjustments to the service so that your queries process as fast as possible\. 

For information, see [Service Quotas](service-limits.md)\. This is an adjustable quota\. You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/athena/quotas) to request a quota increase for concurrent queries\.