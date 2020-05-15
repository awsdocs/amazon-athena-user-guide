# Enabling CloudWatch Query Metrics<a name="athena-cloudwatch-metrics-enable"></a>

When you create a workgroup in the console, the setting for publishing query metrics to CloudWatch is selected by default\.

**To enable or disable query metrics in the Athena console for a workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose the **Workgroup** tab\.

1. Choose the workgroup that you want to modify, and then choose **View details**\.

1. Choose **Edit workgroup**\.

1. On the **Edit workgroup** page, under **Metrics**, select or clear the **Publish query metrics to AWS CloudWatch** option\.

If you use API operations, the command line interface, or the client application with the JDBC driver to create workgroups, to enable publishing of query metrics, set `PublishCloudWatchMetricsEnabled` to `true` in [WorkGroupConfiguration](https://docs.aws.amazon.com/athena/latest/APIReference/API_WorkGroupConfiguration.html)\. The following example shows only the metrics configuration and omits other configuration:

```
"WorkGroupConfiguration": { 
      "PublishCloudWatchMetricsEnabled": "true"
     ....
     }
```