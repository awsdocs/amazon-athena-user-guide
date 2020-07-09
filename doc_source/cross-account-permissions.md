# Cross\-account Access in Athena to Amazon S3 Buckets<a name="cross-account-permissions"></a>

A common Amazon Athena scenario is granting access to users in an account different from the bucket owner so that they can perform queries\. In this case, use a bucket policy to grant access\.

**Note**  
 For information about cross\-account access in AWS Glue, see [Granting Cross\-Account Access](https://docs.aws.amazon.com/glue/latest/dg/cross-account-access.html) in the *[AWS Glue Developer Guide](https://docs.aws.amazon.com/glue/latest/dg/)*\.

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

## Cross\-account Access to a Bucket Encrypted with a Custom AWS KMS Key<a name="cross-account-permissions-kms"></a>

If you have an Amazon S3 bucket that is encrypted with a custom AWS Key Management Service \(AWS KMS\) key, you might need to grant access to it to users from another AWS account\.

Granting access to an AWS KMS\-encrypted bucket in Account A to a user in Account B requires the following permissions:
+ The bucket policy in Account A must grant access to Account B\.
+ The AWS KMS key policy in Account A must grant access to the user in Account B\.
+ The AWS Identity and Access Management \(IAM\) user policy in Account B must grant the user access to both the bucket and the key in Account A\.

The following procedures describe how to grant each of these permissions\.

**To grant access to the bucket in Account A to the user in Account B**
+ From Account A, [review the S3 bucket policy](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html) and confirm that there is a statement that allows access from the account ID of Account B\.

  For example, the following bucket policy allows `s3:GetObject` access to the account ID 111122223333:

  ```
  {
    "Id": "ExamplePolicy1",
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid": "ExampleStmt1",
        "Action": [
          "s3:GetObject"
        ],
        "Effect": "Allow",
        "Resource": "arn:aws:s3:::awsexamplebucket/*",
        "Principal": {
          "AWS": [
            "111122223333"
          ]
        }
      }
    ]
  }
  ```

**To grant access to the user in Account B from the AWS KMS key policy in Account A**

1. In the AWS KMS key policy for Account A, grant the user in Account B permissions to the following actions:
   +  `kms:Encrypt` 
   +  `kms:Decrypt` 
   +  `kms:ReEncrypt*` 
   +  `kms:GenerateDataKey*` 
   +  `kms:DescribeKey` 

   The following example grants key access to only one IAM user or role\.

   ```
   {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::111122223333:role/role_name",
        ]
      },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
   }
   ```

1. From Account A, review the key policy [using the AWS Management Console policy view](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying.html#key-policy-modifying-how-to-console-policy-view)\.

1. In the key policy, verify that the following statement lists Account B as a principal\.

   ```
   "Sid": "Allow use of the key" 
   ```

1. If the `"Sid": "Allow use of the key"` statement is not present, perform the following steps:

   1. Switch to view the key policy [using the console default view](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying.html#key-policy-modifying-how-to-console-default-view)\. 

   1.  Add Account B's account ID as an external account with access to the key\.

**To grant access to the bucket and the key in Account A from the IAM User Policy in Account B**

1. From Account B, open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Open the IAM user or role associated with the user in Account B\.

1. Review the list of permissions policies applied to IAM user or role\.

1. Ensure that a policy is applied that grants access to the bucket\.

   The following example statement grants the IAM user access to the `s3:GetObject` and `s3:PutObject` operations on the bucket `awsexamplebucket`:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "ExampleStmt2",
         "Action": [
           "s3:GetObject",
           "s3:PutObject"
         ],
         "Effect": "Allow",
         "Resource": "arn:aws:s3:::awsexamplebucket/*"
       }
     ]
   }
   ```

1. Ensure that a policy is applied that grants access to the key\.
**Note**  
If the IAM user or role in Account B already has [administrator access](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html), then you don't need to grant access to the key from the user's IAM policies\.

   The following example statement grants the IAM user access to use the key `arn:aws:kms:example-region-1:123456789098:key/111aa2bb-333c-4d44-5555-a111bb2c33dd`\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "ExampleStmt3",
         "Action": [
           "kms:Decrypt",
           "kms:DescribeKey",
           "kms:Encrypt",
           "kms:GenerateDataKey",
           "kms:ReEncrypt*"
         ],
         "Effect": "Allow",
         "Resource": "arn:aws:kms:example-region-1:123456789098:key/111aa2bb-333c-4d44-5555-a111bb2c33dd"
       }
     ]
   }
   ```

For instructions on how to add or correct the IAM user's permissions, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html)\.

## Cross\-account Access to Bucket Objects<a name="cross-account-permissions-objects"></a>

Objects that are uploaded by an account \(Account C\) other than the bucket's owning account \(Account A\) might require explicit object\-level ACLs that grant read access to the querying account \(Account B\)\. To avoid this requirement, Account C should assume a role in Account A before it places objects in Account A’s bucket\. For more information, see [How can I provide cross\-account access to objects that are in Amazon S3 buckets?](https://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/)\.