# Enabling CloudWatch query metrics<a name="athena-cloudwatch-metrics-enable"></a>

When you create a workgroup in the console, the setting for publishing query metrics to CloudWatch is selected by default\.

**To enable or disable query metrics in the Athena console for a workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Workgroups**\.

1. Choose the link of the workgroup that you want to modify\.

1. On the details page for the workgroup, choose **Edit**\.

1. In the **Settings** section, select or clear **Publish query metrics to AWS CloudWatch**\.

If you use API operations, the command line interface, or the client application with the JDBC driver to create workgroups, to enable publishing of query metrics, set `PublishCloudWatchMetricsEnabled` to `true` in [WorkGroupConfiguration](https://docs.aws.amazon.com/athena/latest/APIReference/API_WorkGroupConfiguration.html)\. The following example shows only the metrics configuration and omits other configuration:

```
"WorkGroupConfiguration": { 
      "PublishCloudWatchMetricsEnabled": "true"
     ....
     }
```