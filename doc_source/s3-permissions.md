# Access to Amazon S3<a name="s3-permissions"></a>

If you or your users need to create tables and work with underlying data, they must have access to the Amazon S3 location of the data\. This access is in addition to the allowed actions for Athena that you define in IAM identity\-based polices\.

You can grant access to Amazon S3 locations using identity\-based policies, bucket resource policies, or both\. For detailed information and scenarios about how to grant Amazon S3 access, see [Example Walkthroughs: Managing Access](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access.html) in the *Amazon Simple Storage Service Developer Guide*\. For more information and an example of which Amazon S3 actions to allow, see the example bucket policy in [Cross\-Account Access](cross-account-permissions.md)\.

**Note**  
Athena does not support restricting or allowing access to Amazon S3 resources based on the `aws:SourceIp` condition key\. 