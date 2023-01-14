# Access to Amazon S3<a name="s3-permissions"></a>

You can grant access to Amazon S3 locations using identity\-based policies, bucket resource policies, access point policies, or any combination of the above\. When actors interact with Athena, their permissions pass through Athena to determine what Athena can access\. This means that users must have permission to access Amazon S3 buckets in order to query them with Athena\.

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

**Note**  
You cannot restrict or allow access to Amazon S3 resources based on the `aws:SourceIp`, `aws:SourceVpc`, or `aws:SourceVpce` condition keys\. However, because Athena uses `aws:SourceIp` to access Amazon S3 on your behalf, entries for the related access requests can appear in your Amazon S3 access logs\.

## Amazon S3 access points and access point aliases<a name="s3-permissions-aliases"></a>

If you have a shared dataset in an Amazon S3 bucket, maintaining a single bucket policy that manages access for hundreds of use cases can be challenging\.

Amazon S3 bucket access points help solve this issue\. A bucket can have multiple access points, each with a policy that controls access to the bucket in a different way\. 

For each access point that you create, Amazon S3 generates an alias that represents the access point\. Because the alias is in Amazon S3 bucket name format, you can use the alias in the `LOCATION` clause of your `CREATE TABLE` statements in Athena\. Athena's access to the bucket is then controlled by the policy for the access point that the alias represents\. 

For more information, see [Table location in Amazon S3](tables-location-format.md) and [Using access points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points.html) in the *Amazon S3 User Guide*\.

## Using CalledVia context keys<a name="s3-permissions-calledvia"></a>

For added security, you can use the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-calledvia](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-calledvia) global condition context key\. The `aws:CalledVia` key contains an ordered list of each service in the chain that made requests on the principal's behalf\. By specifying the Athena service principal name `athena.amazonaws.com` for the `aws:CalledVia` context key, you can limit requests to only those made from Athena\. For more information, see [Using Athena with CalledVia context keys](security-iam-athena-calledvia.md)\.

## Additional resources<a name="s3-permissions-additional-resources"></a>

For detailed information and examples about how to grant Amazon S3 access, see the following resources:
+ [Example walkthroughs: Managing access](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access.html) in the *Amazon S3 User Guide*\.
+ [How can I provide cross\-account access to objects that are in Amazon S3 buckets?](http://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/) in the AWS Knowledge Center\.
+ [Cross\-account access in Athena to Amazon S3 buckets](cross-account-permissions.md)\.