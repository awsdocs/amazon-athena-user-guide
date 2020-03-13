# Key Management<a name="key-management"></a>

Amazon Athena supports AWS Key Management Service \(AWS KMS\) to encrypt datasets in Amazon S3 and Athena query results\. AWS KMS uses customer master keys \(CMKs\) to encrypt your Amazon S3 objects and relies on [envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)\. 

In AWS KMS, you can perform the following actions:
+  [Create keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) 
+  [Import your own key material for new CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/importing-keys.html) 

**Note**  
Athena supports only symmetric keys for reading and writing data\.

For more information, see [What is AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide*, and [How Amazon Simple Storage Service Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-s3.html)\. To view the keys in your account that AWS creates and manages for you, in the navigation pane, choose **AWS managed keys**\.

If you are uploading or accessing objects encrypted by SSE\-KMS, use AWS Signature Version 4 for added security\. For more information, see [Specifying the Signature Version in Request Authentication](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingAWSSDK.html#specify-signature-version) in the *Amazon Simple Storage Service Developer Guide*\.