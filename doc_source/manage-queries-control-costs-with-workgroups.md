# Using workgroups to control query access and costs<a name="manage-queries-control-costs-with-workgroups"></a>

Use workgroups to separate users, teams, applications, or workloads, to set limits on amount of data each query or the entire workgroup can process, and to track costs\. Because workgroups act as resources, you can use resource\-level identity\-based policies to control access to a specific workgroup\. You can also view query\-related metrics in Amazon CloudWatch, control costs by configuring limits on the amount of data scanned, create thresholds, and trigger actions, such as Amazon SNS, when these thresholds are breached\. 

To further control costs, you can create capacity reservations with the number of data processing units that you specify and add one or more workgroups to the reservation\. For more information, see [Managing query processing capacity](capacity-management.md)\.

Workgroups integrate with IAM, CloudWatch, Amazon Simple Notification Service, and [AWS Cost and Usage Reports](http://aws.amazon.com/aws-cost-management/aws-cost-and-usage-reporting/) as follows:
+ IAM identity\-based policies with resource\-level permissions control who can run queries in a workgroup\. 
+ Athena publishes the workgroup query metrics to CloudWatch, if you enable query metrics\. 
+ In Amazon SNS, you can create Amazon SNS topics that issue alarms to specified workgroup users when data usage controls for queries in a workgroup exceed your established thresholds\.
+ When you tag a workgroup with a tag configured as a cost allocation tag in the Billing and Cost Management console, the costs associated with running queries in that workgroup appear in your Cost and Usage Reports with that cost allocation tag\.

**Topics**
+ [Using workgroups for running queries](workgroups.md)
+ [Controlling costs and monitoring queries with CloudWatch metrics and events](control-limits.md)

See also the AWS Big Data Blog post [Separate queries and managing costs using Amazon Athena workgroups](http://aws.amazon.com/blogs/big-data/separating-queries-and-managing-costs-using-amazon-athena-workgroups/), which shows you how to use workgroups to separate workloads, control user access, and manage query usage and costs\.