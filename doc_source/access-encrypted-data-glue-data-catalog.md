# Access to Encrypted Metadata in the AWS Glue Data Catalog<a name="access-encrypted-data-glue-data-catalog"></a>

If you use the AWS Glue Data Catalog with Amazon Athena, you can enable encryption in the AWS Glue Data Catalog using the AWS Glue console or the API\. For information, see [Encrypting Your Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html) in the *AWS Glue Developer Guide*\.

If the AWS Glue Data Catalog is encrypted, you must add the following actions to all policies that are used to access Athena:

```
{
 "Version": "2012-10-17",
 "Statement": {
 "Effect": "Allow",
     "Action": [
           "kms:GenerateDataKey",
           "kms:Decrypt",  
           "kms:Encrypt"
      ],
     "Resource": "(arn of key being used to encrypt the catalog)"
   }
}
```

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.