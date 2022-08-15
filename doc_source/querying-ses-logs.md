# Querying Amazon SES event logs<a name="querying-ses-logs"></a>

You can use Amazon Athena to query [Amazon Simple Email Service](http://aws.amazon.com/ses/) \(Amazon SES\) event logs\.

Amazon SES is an email platform that provides a convenient and cost\-effective way to send and receive email using your own email addresses and domains\. You can monitor your Amazon SES sending activity at a granular level using events, metrics, and statistics\.

Based on the characteristics that you define, you can publish Amazon SES events to [Amazon CloudWatch](http://aws.amazon.com/cloudwatch/), [Amazon Kinesis Data Firehose](http://aws.amazon.com/kinesis/data-firehose/), or [Amazon Simple Notification Service](http://aws.amazon.com/sns/)\. After the information is stored in Amazon S3, you can query it from Amazon Athena\. 

For more information about how to analyze Amazon SES email events using Kinesis Data Firehose, Amazon Athena, and Amazon QuickSight, see [Analyzing Amazon SES event data with AWS Analytics Services](http://aws.amazon.com/blogs/messaging-and-targeting/analyzing-amazon-ses-event-data-with-aws-analytics-services/) in the *AWS Messaging and Targeting Blog*\.