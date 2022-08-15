# Monitoring Athena usage metrics<a name="monitoring-athena-usage-metrics"></a>

You can use CloudWatch usage metrics to provide visibility into your how your account uses resources by displaying your current service usage on CloudWatch graphs and dashboards\.

For Athena, usage availability metrics correspond to AWS service quotas for Athena\. You can configure alarms that alert you when your usage approaches a service quota\. For more information about Athena service quotas, see [Service Quotas](service-limits.md)\. For more information about AWS usage metrics, see [AWS usage metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Service-Quota-Integration.html) in the *Amazon CloudWatch User Guide*\.

Athena publishes the following metrics in the `AWS/Usage` namespace\.


|  Metric  |  Description  | 
| --- | --- | 
|  `ActiveQueryCount`  |  The sum of all queued and executing queries for an account, separated by query type \(DML or DDL\)\. Sum is the only useful statistic for this metric\. This metric publishes periodically every minute\. If you are not running any queries, the metric reports nothing \(not even 0\)\. The metric publishes only if active queries are running at the time the metric is taken\.   | 

The following dimensions are used to refine the usage metrics that are published by Athena\.


|  Dimension  |  Description  | 
| --- | --- | 
|  `Service`  |  The name of the AWS service containing the resource\. For Athena, the value for this dimension is `Athena`\.  | 
|  `Type`  |  The type of entity that's being reported\. Currently, the only valid value for Athena usage metrics is `Resource`\.  | 
|  `Resource`  |  The type of resource that is running\. The resource value for Athena query usage is `ActiveQueryCount`\.  | 
|  `Class`  |  The class of resource being tracked\. For Athena, `Class` can be `DML` or `DDL`\.  | 

## Viewing Athena resource usage metrics in the CloudWatch console<a name="monitoring-athena-usage-metrics-cw-console"></a>

You can use the CloudWatch console to see a graph of Athena usage metrics and configure alarms that alert you when your usage approaches a service quota\.

**To view Athena resource usage metrics**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**, **All metrics**\.

1. If you are in the **AWS/Athena**, namespace, choose **All**\.

1. Choose **Usage**, and then choose **By AWS Resource**\.

   The list of service quota usage metrics appears\.

1. Select the check box that is next to **Athena** and **ActiveQueryCount**\.

   The graph above displays your current usage of the AWS resource\.

For information about adding service quotas to the graph and setting an alarm that notifies you if you approach the service quota, see [Visualizing your service quotas and setting alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Quotas-Visualize-Alarms.html) in the *Amazon CloudWatch User Guide*\. For information about setting usage limits per workgroup, see [Setting data usage control limits](workgroups-setting-control-limits-cloudwatch.md)\.