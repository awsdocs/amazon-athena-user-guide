# Access to Amazon S3<a name="s3-permissions"></a>

You can grant access to Amazon S3 locations using identity\-based policies, bucket resource policies, access point policies, or any combination of the above\. 

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

**Note**  
Athena does not support restricting or allowing access to Amazon S3 resources based on the `aws:SourceIp`, `aws:SourceVpc`, or `aws:SourceVpce` condition keys\.

## Amazon S3 Access Points and Access Point Aliases<a name="s3-permissions-aliases"></a>

If you have a shared dataset in an Amazon S3 bucket, maintaining a single bucket policy that manages access for hundreds of use cases can be challenging\.

Amazon S3 bucket access points help solve this issue\. A bucket can have multiple access points, each with a policy that controls access to the bucket in a different way\. 

For each access point that you create, Amazon S3 generates an alias that represents the access point\. Because the alias is in Amazon S3 bucket name format, you can use the alias in the `LOCATION` clause of your `CREATE TABLE` statements in Athena\. Athena's access to the bucket is then controlled by the policy for the access point that the alias represents\. 

For more information, see [Table Location in Amazon S3](tables-location-format.md) and [Using access points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points.html) in the *Amazon S3 User Guide*\.

## Additional Resources<a name="s3-permissions-additional-resources"></a>

For detailed information and examples about how to grant Amazon S3 access, see the following resources:
+ [Example Walkthroughs: Managing Access](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access.html) in the *Amazon S3 User Guide*\.
+ [How can I provide cross\-account access to objects that are in Amazon S3 buckets?](http://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/) in the AWS Knowledge Center\.
+ [Cross\-account Access in Athena to Amazon S3 Buckets](cross-account-permissions.md)\.