# Monitoring Apache Spark calculations with CloudWatch metrics<a name="notebooks-spark-metrics"></a>

Athena publishes calculation\-related metrics to Amazon CloudWatch when the **[Publish CloudWatch metrics](notebooks-spark-getting-started.md#notebook-gs-metrics)** option for your Spark\-enabled workgroup is selected\. You can create custom dashboards, set alarms and triggers on metrics in the CloudWatch console\. 

Athena publishes the following metric to the CloudWatch console under the `AmazonAthenaForApacheSpark` namespace:
+ `DPUCount` – number of DPUs consumed during the session to execute the calculations\.

This metric has the following dimensions:
+ `SessionId` – The ID of the session in which the calculations are submitted\.
+ `WorkGroup` – Name of the workgroup\.

**To view metrics for Spark\-enabled workgroups in the Amazon CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**, **All metrics**\.

1. Select the **AmazonAthenaForApacheSpark** namespace\.

**To view metrics with the CLI**
+ Do one of the following:
  + To list the metrics for Athena Spark\-enabled workgroups, open a command prompt, and use the following command:

    ```
    aws cloudwatch list-metrics --namespace "AmazonAthenaForApacheSpark"
    ```
  + To list all available metrics, use the following command:

    ```
    aws cloudwatch list-metrics"
    ```

## List of CloudWatch metrics and dimensions for Apache Spark calculations in Athena<a name="notebooks-spark-metrics-metrics-table"></a>

If you've enabled CloudWatch metrics in your Spark\-enabled Athena workgroup, Athena sends the following metric to CloudWatch per workgroup\. The metric uses the `AmazonAthenaForApacheSpark` namespace\.


****  

| Metric name | Description | 
| --- | --- | 
| DPUCount  | Number of DPUs \(data processing units\) consumed during the session to execute the calculations\. A DPU is a relative measure of processing power that consists of 4 vCPUs of compute capacity and 16 GB of memory\. | 

This metric has the following dimensions\.


| Dimension | Description | 
| --- | --- | 
| SessionId |  The ID of the session in which the calculations are submitted\.  | 
| WorkGroup |  The name of the workgroup\.  | 