# Using Workgroups to Control Query Access and Costs<a name="manage-queries-control-costs-with-workgroups"></a>

Use workgroups to separate users, teams, applications, or workloads, to set limits on amount of data each query or the entire workgroup can process, and to track costs\. Because workgroups act as resources, you can use resource\-level identity\-based policies to control access to a specific workgroup\. You can also view query\-related metrics in Amazon CloudWatch, control costs by configuring limits on the amount of data scanned, create thresholds, and trigger actions, such as Amazon SNS, when these thresholds are breached\. 

Workgroups integrate with IAM, CloudWatch, and Amazon Simple Notification Service as follows:
+ IAM identity\-based policies with resource\-level permissions control who can run queries in a workgroup\. 
+ Athena publishes the workgroup query metrics to CloudWatch, if you enable query metrics\. 
+ In Amazon SNS, you can create Amazon SNS topics that issue alarms to specified workgroup users when data usage controls for queries in a workgroup exceed your established thresholds\.

**Topics**
+ [Using Workgroups for Running Queries](workgroups.md)
+ [Controlling Costs and Monitoring Queries with CloudWatch Metrics and Events](control-limits.md)

See also the AWS Big Data Blog post [Separate queries and managing costs using Amazon Athena workgroups](http://aws.amazon.com/blogs/big-data/separating-queries-and-managing-costs-using-amazon-athena-workgroups/), which shows you how to use workgroups to separate workloads, control user access, and manage query usage and costs\.