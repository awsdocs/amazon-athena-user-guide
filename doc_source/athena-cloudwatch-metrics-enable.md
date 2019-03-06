# Enabling CloudWatch Query Metrics<a name="athena-cloudwatch-metrics-enable"></a>

When you create a workgroup in the console, the setting for publishing query metrics to CloudWatch is selected by default\.

If you use API operations, the command line interface, or the client application with the JDBC driver to create workgroups, to enable publishing of query metrics, set `PublishCloudWatchMetricsEnabled` to `true` in [WorkGroupConfiguration](https://docs.aws.amazon.com/athena/latest/APIReference/API_WorkGroupConfiguration.html)\. The following example shows only the metrics configuration and omits other configuration:

```
"WorkGroupConfiguration": { 
      "PublishCloudWatchMetricsEnabled": "true"
     ....
     }
```