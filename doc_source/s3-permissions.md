# Access to Amazon S3<a name="s3-permissions"></a>

You can grant access to Amazon S3 locations using identity\-based policies, bucket resource policies, or both\. 

For detailed information and examples about how to grant Amazon S3 access, see the following resources:
+ [Example Walkthroughs: Managing Access](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access.html) in the *Amazon Simple Storage Service Developer Guide*\.
+ [How can I provide cross\-account access to objects that are in Amazon S3 buckets?](http://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/) in the AWS Knowledge Center\.
+ [Cross\-account Access in Athena to Amazon S3 Buckets](cross-account-permissions.md)\.

**Note**  
Athena does not support restricting or allowing access to Amazon S3 resources based on the `aws:SourceIp` condition key\. 