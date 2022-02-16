# February 15, 2022<a name="release-note-2022-02-15"></a>

Published on 2022\-02\-15

Amazon Athena has increased the active DML query quota in all AWS Regions\. Active queries include both running and queued queries\. With this change, you can now have more DML queries in an active state than before\.

For information on Athena service quotas, see [Service Quotas](service-limits.md)\. For the query quotas in the Region where you use Athena, see [Amazon Athena endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/athena.html#amazon-athena-limits) in the *AWS General Reference*\.

To monitor your quota usage, you can use CloudWatch usage metrics\. Athena publishes the `ActiveQueryCount` metric in the `AWS/Usage` namespace\. For more information, see [Monitoring Athena Usage Metrics](monitoring-athena-usage-metrics.md)\.

After reviewing your usage, you can use the [Service Quotas](https://console.aws.amazon.com/servicequotas/) console to request a quota increase\. If you previously requested a quota increase for your account, your requested quota still applies if it exceeds the new default active DML query quota\. Otherwise, all accounts use the new default\.