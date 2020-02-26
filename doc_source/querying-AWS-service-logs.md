# Querying AWS Service Logs<a name="querying-AWS-service-logs"></a>

This section includes several procedures for using Amazon Athena to query popular datasets, such as AWS CloudTrail logs, Amazon CloudFront logs, Classic Load Balancer logs, Application Load Balancer logs, Amazon VPC flow logs, andNetwork Load Balancer logs\.

The tasks in this section use the Athena console, but you can also use other tools that connect via JDBC\. For more information, see [Using Athena with the JDBC Driver](connect-with-jdbc.md), the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/athena/), or the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\.

The topics in this section assume that you have set up both an IAM user with appropriate permissions to access Athena and the Amazon S3 bucket where the data to query should reside\. For more information, see [Setting Up](setting-up.md) and [Getting Started](getting-started.md)\.

**Topics**
+ [Querying Application Load Balancer Logs](application-load-balancer-logs.md)
+ [Querying Classic Load Balancer Logs](elasticloadbalancer-classic-logs.md)
+ [Querying Amazon CloudFront Logs](cloudfront-logs.md)
+ [Querying AWS CloudTrail Logs](cloudtrail-logs.md)
+ [Querying Amazon EMR Logs](emr-logs.md)
+ [Querying AWS Global Accelerator Flow Logs](querying-global-accelerator-flow-logs.md)
+ [Querying Network Load Balancer Logs](networkloadbalancer-classic-logs.md)
+ [Querying Amazon VPC Flow Logs](vpc-flow-logs.md)
+ [Querying AWS WAF Logs](waf-logs.md)