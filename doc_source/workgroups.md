# Using Workgroups for Running Queries<a name="workgroups"></a>

Workgroups allow you to:


|  |  | 
| --- |--- |
| Isolate users, teams, applications, or workloads into groups\.  |  Each workgroup has its own distinct query history and a list of saved queries\. For more information, see [How Workgroups Work](user-created-workgroups.md)\. For all queries in the workgroup, you can choose to configure workgroup settings\. They include an Amazon S3 location for storing query results, and encryption configuration\. You can also enforce workgroup settings\. For more information, see [Workgroup Settings](workgroups-settings.md)\.   | 
|  Enforce costs constraints\.  |  You can set two types of cost constraints for queries in a workgroup:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/workgroups.html)For detailed steps, see [Setting Data Usage Control Limits](workgroups-setting-control-limits-cloudwatch.md)\. | 
|  Track query\-related metrics for all workgroup queries in CloudWatch\.   |  For each query that runs in a workgroup, if you configure the workgroup to publish metrics, Athena publishes `Query Type`, `Query Execution Time`, `Query Status`, `Data Processed Per Query`, and `Workgroup Name` to CloudWatch\. You can also [view query metrics](query-metrics-viewing.md) for each of your workgroups within the Athena console\.  In CloudWatch, you can create custom dashboards, and set thresholds and alarms on these metrics\.  | 

**Topics**
+ [How Workgroups Work](user-created-workgroups.md)
+ [Setting up Workgroups](workgroups-procedure.md)
+ [IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)
+ [Workgroup Example Policies](example-policies-workgroup.md)
+ [Workgroup Settings](workgroups-settings.md)
+ [Managing Workgroups](workgroups-create-update-delete.md)
+ [Athena Workgroup APIs](workgroups-api-list.md)
+ [Troubleshooting Workgroups](workgroups-troubleshooting.md)