# Benefits of using workgroups<a name="workgroups-benefits"></a>

Workgroups allow you to:


|  |  | 
| --- |--- |
| Isolate users, teams, applications, or workloads into groups\.  |  Each workgroup has its own distinct query history and a list of saved queries\. For more information, see [How workgroups work](user-created-workgroups.md)\. For all queries in the workgroup, you can choose to configure workgroup settings\. They include an Amazon S3 location for storing query results, expected bucket owner, encryption, and control of objects written to the query results bucket\. You can also enforce workgroup settings\. For more information, see [Workgroup settings](workgroups-settings.md)\.   | 
|  Enforce costs constraints\.  |  You can set two types of cost constraints for queries in a workgroup:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/workgroups-benefits.html)For detailed steps, see [Setting data usage control limits](workgroups-setting-control-limits-cloudwatch.md)\. | 
|  Track query\-related metrics for all workgroup queries in CloudWatch\.   |  For each query that runs in a workgroup, if you configure the workgroup to publish metrics, Athena publishes them to CloudWatch\. You can [view query metrics](query-metrics-viewing.md) for each of your workgroups within the Athena console\. In CloudWatch, you can create custom dashboards, and set thresholds and alarms on these metrics\.  | 