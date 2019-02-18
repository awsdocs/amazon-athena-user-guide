# Enabling Query Metrics<a name="athena-cloudwatch-metrics-enable"></a>

When you create a workgroup in the console, the setting for publishing query metrics to CloudWatch is selected by default\.

If you use API operations, the command line interface, or the client application with the JDBC driver to create workgroups, you must enable publishing of query metrics by setting the value for `PublishCloudWatchMetricsEnabled` in the `WorkGroupConfiguration` parameter of the `CreateWorkgroup` API:

PublishCloudWatchMetricsEnabled  
Set the value of this optional parameter to `true`\.

This value is passed to the `WorkGroupConfiguration` parameter in the API, as in the following abbreviated example\. This example shows only the metrics configuration and omits other configuration\.

```
"WorkGroupConfiguration": { 
      "PublishCloudWatchMetricsEnabled": "true"
     ....
     }
```