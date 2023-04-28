# Determining capacity requirements<a name="capacity-management-requirements"></a>

Before you create a capacity reservation, you can estimate the capacity required so that you can assign it the correct number of DPUs\. And, after a reservation is in use, you might want to check the reservation for insufficient or excess capacity\. This topic describes techniques that you can use to make these estimates and also describes some AWS tools for assessing usage and cost\.

**Topics**
+ [Estimating required capacity](#capacity-management-requirements-estimating)
+ [Signs that more capacity is required](#capacity-management-requirements-insufficient-capacity)
+ [Checking for idle capacity](#capacity-management-requirements-idle-capacity)
+ [Tools for assessing capacity requirements and cost](#capacity-management-requirements-tools)

## Estimating required capacity<a name="capacity-management-requirements-estimating"></a>

When estimating capacity requirements, it is useful to consider two perspectives: how much capacity a particular query might require, and how much capacity you might need in general\.

### Estimating per\-query capacity requirements<a name="capacity-management-requirements-estimating-query"></a>

To determine the number of DPUs that a query might would require, you can use the following guidelines:
+ DDL queries consume 4 DPUs\.
+ DML queries typically consume between 4 and 124 DPUs\.

Athena determines the number of DPUs required by a DML query when the query is submitted\. The number varies based on data size, storage format, query construction, and other factors\. Generally, Athena tries to select the lowest, most efficient DPU number\. If Athena determines that more computational power is required for the query to complete successfully, it increases the number of DPUs assigned to the query\.

### Estimating workload specific capacity requirements<a name="capacity-management-requirements-estimating-workload"></a>

To determine how much capacity you might require to run multiple queries at the same time, consider the general guidelines in the following table:


****  

| Concurrent queries | DPUs required | 
| --- | --- | 
| 10 | 40 | 
| 20 | 96 | 
| 30 or more | 240 | 

As a rule of thumb, to run 10 concurrent queries, start with at least 40 DPUs\. For 20 concurrent queries, use 96, and for 30 or more concurrent queries, plan on using 240 DPUs\.

Note that the actual number of DPUs that you need depends on your goals and analysis patterns\. For example, if you want queries to start immediately without queuing, determine your peak concurrent query demand, and then provision the number of DPUs accordingly\.

You can provision fewer DPUs than your peak demand, but queuing may result when peak demand occurs\. When queuing occurs, Athena holds your queries in a queue and runs them when capacity becomes available\.

If your goal is to run queries within a fixed budget, you can use the [AWS Pricing Calculator](https://calculator.aws/#/addService/Athena) to determine the number of DPUs required\.

Lastly, remember that data size, storage format, and how a query is written influence the DPUs that a query requires\. To increase query performance, you can compress or partition your data or convert it into columnar formats\. For more information, see [Performance tuning in Athena](performance-tuning.md)\.

## Signs that more capacity is required<a name="capacity-management-requirements-insufficient-capacity"></a>

Insufficient capacity error messages and query queuing are two indications that your assigned capacity is inadequate\.

If your queries fail with an insufficient capacity error message, your capacity reservation's DPU count is probably too low for your query workload\. For example, if you have a reservation with 24 DPUs and run a query that requires more than 24 DPUs, the query will fail\. To monitor for this query error, you can use Athena's [CloudWatch Events](#capacity-management-requirements-tools-cloudwatch-events-and-eventbridge)\. Try adding more DPUs and then re\-running your query\.

If many queries are queued, it means your capacity is fully utilized by other queries\. To reduce the queuing, do one of the following:
+ Add DPUs to your reservation to increase query concurrency\.
+ Remove workgroups from your reservation to free up capacity for other queries\.

To check for excessive query queuing, use the Athena query queue time [CloudWatch metric](#capacity-management-requirements-tools-cloudwatch-metrics) for the workgroups in your capacity reservation\. If the value is above your preferred threshold, you can add DPUs to the capacity reservation\.

## Checking for idle capacity<a name="capacity-management-requirements-idle-capacity"></a>

To check for idle capacity, you can either decrease the number of DPUs in the reservation or increase its workload, and then observe the results\.

**To check for idle capacity**

1. Do one of the following:
   + Reduce the number of DPUs in your reservation \(reduce the resources available\)
   + Add workgroups to your reservation \(increase the workload\)

1. Use [CloudWatch](#capacity-management-requirements-tools-cloudwatch-metrics) to measure the query queue time\.

1. If the queue time increases beyond a desirable level, do one of the following
   + Remove workgroups
   + Add DPUs to your capacity reservation

1. After each change, check the performance and query queue time\.

1. Continue to adjust the workload and/or DPU count to attain the desired balance\.

If you do not want to maintain capacity outside a preferred time period, you can [cancel](capacity-management-managing-reservations.md#capacity-management-cancelling-a-capacity-reservation) the reservation and create another reservation later\. However, even if you recently cancelled capacity from another reservation, requests for new capacity are not guaranteed, and new reservations take time to create\.

## Tools for assessing capacity requirements and cost<a name="capacity-management-requirements-tools"></a>

You can use the following services and features in AWS to measure your Athena usage and costs\.

### CloudWatch metrics<a name="capacity-management-requirements-tools-cloudwatch-metrics"></a>

You can configure Athena to publish query\-related metrics to Amazon CloudWatch at the workgroup level\. After you enable metrics for the workgroup, the metrics for the workgroup's queries are displayed in the Athena console on the workgroup's details page\.

For information about the Athena metrics published to CloudWatch and their dimensions, see [Monitoring Athena queries with CloudWatch metrics](query-metrics-viewing.md)\.

### CloudWatch usage metrics<a name="capacity-management-requirements-tools-cloudwatch-usage-metrics"></a>

You can use CloudWatch usage metrics to provide visibility into your how your account uses resources by displaying your current service usage on CloudWatch graphs and dashboards\. For Athena, usage availability metrics correspond to AWS [service quotas](service-limits.md) for Athena\. You can configure alarms that alert you when your usage approaches a service quota\.

For more information, see [Monitoring Athena usage metrics](monitoring-athena-usage-metrics.md)\.

### CloudWatch Events and Amazon EventBridge<a name="capacity-management-requirements-tools-cloudwatch-events-and-eventbridge"></a>

You can use Amazon Athena with Amazon CloudWatch to receive real\-time notifications regarding the state of your queries\. When a query you have submitted changes states, Athena publishes an event to CloudWatch Events that contains information about the query state transition\. You can write simple rules for events that are of interest to you and take automated actions when an event matches a rule\.

CloudWatch Events and Amazon EventBridge are the same underlying service and API, but EventBridge provides more features and is the preferred way to manage your events\. Changes you make in either CloudWatch or EventBridge appear in each console\.

For more information, see the following resources\.
+ [Monitoring Athena queries with CloudWatch events](athena-cloudwatch-events.md)
+ [What Is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)
+ [What Is Amazon CloudWatch Events?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)

### Tags<a name="capacity-management-requirements-tools-tags"></a>

In Athena, capacity reservations support tags\. A tag consists of a key and a value\. To track your costs in Athena, you can use AWS\-generated cost allocation tags\. AWS uses the cost allocation tags to organize your resource costs on your [Cost and Usage Report](https://docs.aws.amazon.com/cur/latest/userguide/what-is-cur.html)\. This makes it easier for you to categorize and track your AWS costs\. To activate cost allocation tags for Athena, you use the [AWS Billing and Cost Management console](https://console.aws.amazon.com/billing/)\.

For more information, see the following resources\.
+ [Tagging Athena resources](tags.md)
+ [Activating the AWS\-generated cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activate-built-in-tags.html)
+ [Using AWS cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html)