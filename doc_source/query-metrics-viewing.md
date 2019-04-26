# Monitoring Athena Queries with CloudWatch Metrics<a name="query-metrics-viewing"></a>

Athena publishes query\-related metrics to Amazon CloudWatch, when **Publish to CloudWatch** is selected\. You can create custom dashboards, set alarms and triggers on metrics in CloudWatch, or use pre\-populated dashboards directly from the Athena console\. 

When you enable query metrics for queries in workgroups, the metrics are displayed within the **Metrics** tab in the **Workgroups** panel, for each workgroup in the Athena console\.

Athena publishes the following metrics to the CloudWatch console:
+ `Query Status` \(successful, failed, or canceled\)
+ `Query Execution Time` \(in seconds\)
+ `Query Type` \(DDL or DML\)
+ `Data Processed Per Query`\. This is the total amount of data scanned per query \(in Megabytes\)\.
+ `Workgroup Name`

**To view query metrics for a workgroup in the console**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose the **Workgroup:*<name>*** tab\.

   To view a workgroup's metrics, you don't need to switch to it and can remain in another workgroup\. You do need to select the workgroup from the list\. You also must have permissions to view its metrics\.

1. Select the workgroup from the list, and then choose **View details**\. If you have permissions, the workgroup's details display in the **Overview** tab\.

1. Choose the **Metrics** tab\.   
![\[The workgroup metrics screenshot.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-metrics.png)

   As a result, the metrics display\.

1. Choose the metrics interval that Athena should use to fetch the query metrics from CloudWatch, or choose the refresh icon to refresh the displayed metrics\.  
![\[The screenshot of query metrics intervals.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-metric-intervals.png)

**To view metrics in the Amazon CloudWatch console**

1. Open the Amazon CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch)\.

1. In the navigation pane, choose **Metrics**\.

1. Select the `AWS/Athena` namespace\.

**To view metrics with the CLI**
+ Open a command prompt, and use the following command:

  ```
  aws cloudwatch list-metrics --namespace "AWS/Athena"
  ```
+ To list all available metrics, use the following command:

  ```
  aws cloudwatch list-metrics --namespace "AWS/Athena"
  ```

## List of CloudWatch Metrics for Athena<a name="athena-cloudwatch-metrics-table"></a>

If you've enabled CloudWatch metrics in Athena, it sends the following metrics to CloudWatch\. The metrics use the `AWS/Athena` namespace\.


| Metric Name | Description | 
| --- | --- | 
| Total amount of data scanned per query |  The amount of data in Megabytes that Athena scanned per query\.  | 
| Query state |  The query state\. Valid statistics: Successful, Failed, Canceled  | 
| Total query execution time |  The amount of time in seconds it takes Athena to run the query\.  | 
| Query type |  The query type\. Valid statistics: DDL or DML\.  | 
| Workgroup name |  The name of the workgroup\.  | 