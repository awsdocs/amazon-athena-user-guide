# Querying AWS service logs<a name="querying-aws-service-logs"></a>

This section includes several procedures for using Amazon Athena to query popular datasets, such as AWS CloudTrail logs, Amazon CloudFront logs, Classic Load Balancer logs, Application Load Balancer logs, Amazon VPC flow logs, and Network Load Balancer logs\.

The tasks in this section use the Athena console, but you can also use other tools like the [Athena JDBC driver](connect-with-jdbc.md), the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/athena/), or the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\.

For information about using AWS CloudFormation to automatically create AWS service log tables, partitions, and example queries in Athena, see [Automating AWS service logs table creation and querying them with Amazon Athena](http://aws.amazon.com/blogs/big-data/automating-aws-service-logs-table-creation-and-querying-them-with-amazon-athena/) in the AWS Big Data Blog\. For information about using a Python library for AWS Glue to create a common framework for processing AWS service logs and querying them in Athena, see [Easily query AWS service logs using Amazon Athena](http://aws.amazon.com/blogs/big-data/easily-query-aws-service-logs-using-amazon-athena/)\.

The topics in this section assume that you have configured appropriate permissions to access Athena and the Amazon S3 bucket where the data to query should reside\. For more information, see [Setting up](setting-up.md) and [Getting started](getting-started.md)\.

**Topics**
+ [Application Load Balancer](application-load-balancer-logs.md)
+ [Elastic Load Balancing](elasticloadbalancer-classic-logs.md)
+ [CloudFront](cloudfront-logs.md)
+ [CloudTrail](cloudtrail-logs.md)
+ [Amazon EMR](emr-logs.md)
+ [Global Accelerator](querying-global-accelerator-flow-logs.md)
+ [GuardDuty](querying-guardduty.md)
+ [Network Firewall](querying-network-firewall-logs.md)
+ [Network Load Balancer](networkloadbalancer-classic-logs.md)
+ [Route 53](querying-r53-resolver-logs.md)
+ [Amazon SES](querying-ses-logs.md)
+ [Amazon VPC](vpc-flow-logs.md)
+ [AWS WAF](waf-logs.md)

For information about querying Amazon S3 logs, see the following topics:
+ [How do I analyze my Amazon S3 server access logs using Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/analyze-logs-athena/) in the AWS Knowledge Center
+ [Querying Amazon S3 access logs for requests using Amazon Athena](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-s3-access-logs-to-identify-requests.html#querying-s3-access-logs-for-requests) in the Amazon Simple Storage Service User Guide
+ [Using AWS CloudTrail to identify Amazon S3 requests](https://docs.aws.amazon.com/AmazonS3/latest/dev/cloudtrail-request-identification.html) in the Amazon Simple Storage Service User Guide