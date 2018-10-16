# Access to Encrypted Metadata in the AWS Glue Data Catalog<a name="access-encrypted-data-glue-data-catalog"></a>

The encryption of objects in the AWS Glue Data Catalog is available only if you have upgraded to using AWS Glue with Athena\. 

You can optionally enable encryption in the AWS Glue Data Catalog using the AWS Glue console, or the API\. For information, see [Encrypting Your Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html) in the *AWS Glue Developer Guide*\.

 If you encrypt your AWS Glue Data Catalog, you must add the following actions to all of your policies used to access Athena:

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