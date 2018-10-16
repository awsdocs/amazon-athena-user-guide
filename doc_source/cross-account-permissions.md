# Cross\-account Access<a name="cross-account-permissions"></a>

A common scenario is granting access to users in an account different from the bucket owner so that they can perform queries\. In this case, use a bucket policy to grant access\.

The following example bucket policy, created and applied to bucket `s3://my-athena-data-bucket` by the bucket owner, grants access to all users in account `123456789123`, which is a different account\.

```
{
   "Version": "2012-10-17",
   "Id": "MyPolicyID",
   "Statement": [
      {
          "Sid": "MyStatementSid",
          "Effect": "Allow",
          "Principal": {
             "AWS": "arn:aws:iam::123456789123:root"
          },
          "Action": [
             "s3:GetBucketLocation",
             "s3:GetObject",
             "s3:ListBucket",
             "s3:ListBucketMultipartUploads",
             "s3:ListMultipartUploadParts",
             "s3:AbortMultipartUpload",
             "s3:PutObject"
          ],
          "Resource": [
             "arn:aws:s3:::my-athena-data-bucket",
             "arn:aws:s3:::my-athena-data-bucket/*"
          ]
       }
    ]
 }
```

To grant access to a particular user in an account, replace the `Principal` key with a key that specifies the user instead of `root`\. For example, for user profile `Dave`, use `arn:aws:iam::123456789123:user/Dave`\.