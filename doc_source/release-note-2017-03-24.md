# March 24, 2017<a name="release-note-2017-03-24"></a>

Published on *2017\-03\-24*

Added the AWS CloudTrail SerDe, improved performance, fixed partition issues\.

## Features<a name="release-note-2017-03-24-features"></a>
+ Added the AWS CloudTrail SerDe\. For more information, see [CloudTrail SerDe](cloudtrail.md)\. For detailed usage examples, see the AWS Big Data Blog post, [ Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\.

## Improvements<a name="release-note-2017-03-24-improvements"></a>
+ Improved performance when scanning a large number of partitions\.
+ Improved performance on `MSCK Repair Table` operation\.
+ Added ability to query Amazon S3 data stored in regions other than your primary region\. Standard inter\-region data transfer rates for Amazon S3 apply in addition to standard Athena charges\.

## Bug Fixes<a name="release-note-2017-03-24-bug-fixes"></a>
+ Fixed a bug where a "table not found error" might occur if no partitions are loaded\.
+ Fixed a bug to avoid throwing an exception with `ALTER TABLE ADD PARTITION IF NOT EXISTS` queries\.
+ Fixed a bug in `DROP PARTITIONS`\.