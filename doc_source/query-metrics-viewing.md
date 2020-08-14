# Monitoring Athena Queries with CloudWatch Metrics<a name="query-metrics-viewing"></a>

Athena publishes query\-related metrics to Amazon CloudWatch, when **Publish to CloudWatch** is selected\. You can create custom dashboards, set alarms and triggers on metrics in CloudWatch, or use pre\-populated dashboards directly from the Athena console\. 

When you enable query metrics for queries in workgroups, the metrics are displayed within the **Metrics** tab in the **Workgroups** panel, for each workgroup in the Athena console\.

Athena publishes the following metrics to the CloudWatch console:
+ `EngineExecutionTime` – in milliseconds
+ `ProcessedBytes` – the total amount of data scanned per DML query
+ `QueryPlanningTime` – in milliseconds
+ `QueryQueueTime` – in milliseconds
+ `ServiceProcessingTime` – in milliseconds
+ `TotalExecutionTime` – in milliseconds, for DDL and DML queries

These metrics have the following dimensions:
+ `QueryState` – `QUEUED`, `RUNNING`, `SUCCEEDED`, `FAILED`, or `CANCELLED`
+ `QueryType` – `DML` or `DDL`
+ `WorkGroup` – name of the workgroup

For more information, see the [List of CloudWatch Metrics and Dimensions for Athena](#athena-cloudwatch-metrics-table) later in this topic\.

**To view query metrics for a workgroup in the console**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose the **Workgroup:*<name>*** tab\.

   To view a workgroup's metrics, you don't need to switch to it and can remain in another workgroup\. You do need to select the workgroup from the list\. You also must have permissions to view its metrics\.

1. Select the workgroup from the list, and then choose **View details**\. If you have permissions, the workgroup's details display in the **Overview** tab\.

1. Choose the **Metrics** tab\.   
![\[Choose the Metrics tab on the details page for a workgroup in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-metrics.png)

   The metrics dashboard displays\.
**Note**  
If you just recently enabled metrics for the workgroup and/or there has been no recent query activity, the graphs on the dashboard may be empty\. Query activity is retrieved from CloudWatch depending on the interval that you specify in the next step\. 

1. Choose the metrics interval that Athena should use to fetch the query metrics from CloudWatch, or specify a custom interval\.  
![\[Specifying the metrics retrieval interval for a workgroup in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-custom-interval.png)

1. To refresh the displayed metrics, choose the refresh icon\.  
![\[Choose the refresh icon.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-refresh-metrics.png)

1. Click the down arrow next to the refresh icon to choose the **Auto refresh** option and a refresh interval for the metrics display\.  
![\[Choosing a refresh interval for the workgroup metrics display in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-choose-refresh-interval.png)

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

## List of CloudWatch Metrics and Dimensions for Athena<a name="athena-cloudwatch-metrics-table"></a>

If you've enabled CloudWatch metrics in Athena, it sends the following metrics to CloudWatch per workgroup\. The metrics use the `AWS/Athena` namespace\.


| Metric Name | Description | 
| --- | --- | 
| EngineExecutionTime |  The number of milliseconds that the query took to run\.  | 
| ProcessedBytes |  The amount of data in megabytes that Athena scanned per DML query\. For queries that were canceled \(either by the users, or automatically, if they reached the limit\), this includes the amount of data scanned before the cancellation time\. This metric is not reported for DDL or CTAS queries\.  | 
| QueryPlanningTime | The number of milliseconds that Athena took to plan the query processing flow\. This includes the time spent retrieving table partitions from the data source\. Note that because the query engine performs the query planning, query planning time is a subset of EngineExecutionTime\. | 
| QueryQueueTime | The number of milliseconds that the query was in the query queue waiting for resources\. Note that if transient errors occur, the query can be automatically added back to the queue\. | 
| ServiceProcessingTime | Number of milliseconds that Athena took to process the query results after the query engine finished running the query\. | 
| TotalExecutionTime | The number of milliseconds that Athena took to run a DDL or DML query\. TotalExecutionTime includes QueryQueueTime, QueryPlanningTime, EngineExecutionTime, and ServiceProcessingTime\. | 

CloudWatch metrics for Athena have the following dimensions\.


| Dimension | Description | 
| --- | --- | 
| QueryState |  The query state\. Valid statistics: QUEUED, RUNNING, SUCCEEDED, FAILED, or CANCELLED\.  Athena automatically retries your queries in cases of certain transient errors\. As a result, you may see the query state transition from RUNNING or FAILED to QUEUED\.   | 
| QueryType |  The query type\. Valid statistics: `DDL` or `DML`\.  | 
| WorkGroup |  The name of the workgroup\.  | 