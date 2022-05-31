# Encryption at rest<a name="encryption"></a>

You can run queries in Amazon Athena on encrypted data in Amazon S3 in the same Region and across a limited number of Regions\. You can also encrypt the query results in Amazon S3 and the data in the AWS Glue Data Catalog\.

You can encrypt the following assets in Athena:
+ The results of all queries in Amazon S3, which Athena stores in a location known as the Amazon S3 results location\. You can encrypt query results stored in Amazon S3 whether the underlying dataset is encrypted in Amazon S3 or not\. For information, see [Encrypting Athena query results stored in Amazon S3Encrypting Athena query results when using JDBC or ODBC](encrypting-query-results-stored-in-s3.md)\.
+ The data in the AWS Glue Data Catalog\. For information, see [Permissions to encrypted metadata in the AWS Glue Data Catalog](#glue-encryption)\.

**Note**  
The setup for querying an encrypted dataset in Amazon S3 and the options in Athena to encrypt query results are independent\. Each option is enabled and configured separately\. You can use different encryption methods or keys for each\. This means that reading encrypted data in Amazon S3 doesn't automatically encrypt Athena query results in Amazon S3\. The opposite is also true\. Encrypting Athena query results in Amazon S3 doesn't encrypt the underlying dataset in Amazon S3\.

**Topics**
+ [Supported Amazon S3 encryption options](#encryption-options-S3-and-Athena)
+ [Permissions to encrypted data in Amazon S3](#permissions-for-encrypting-and-decrypting-data)
+ [Permissions to encrypted metadata in the AWS Glue Data Catalog](#glue-encryption)
+ [Encrypting Athena query results stored in Amazon S3](encrypting-query-results-stored-in-s3.md)
+ [Creating tables based on encrypted datasets in Amazon S3](creating-tables-based-on-encrypted-datasets-in-s3.md)

## Supported Amazon S3 encryption options<a name="encryption-options-S3-and-Athena"></a>

Athena supports the following encryption options for datasets and query results in Amazon S3\.


| Encryption type | Description | Cross\-Region support | 
| --- | --- | --- | 
| [SSE\-S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html) | Server side encryption \(SSE\) with an Amazon S3\-managed key\. | Yes | 
| [SSE\-KMS](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html) | Server\-side encryption \(SSE\) with a AWS Key Management Service customer managed key\.  With this encryption type, Athena does not require you to indicate that data is encrypted when you create a table\.  | Yes | 
| [CSE\-KMS](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingClientSideEncryption.html#client-side-encryption-kms-managed-master-key-intro) |  Client\-side encryption \(CSE\) with a AWS KMS customer managed key\. In Athena, this option requires that you use a `CREATE TABLE` statement with a `TBLPROPERTIES` clause that specifies `'has_encrypted_data'='true'`\. For more information, see [Creating tables based on encrypted datasets in Amazon S3](creating-tables-based-on-encrypted-datasets-in-s3.md)\.  | No | 

For more information about AWS KMS encryption with Amazon S3, see [What is AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) and [How Amazon Simple Storage Service \(Amazon S3\) uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-s3.html) in the *AWS Key Management Service Developer Guide*\. For more information about using SSE\-KMS or CSE\-KMS with Athena, see [Launch: Amazon Athena adds support for querying encrypted data](http://aws.amazon.com/blogs/aws/launch-amazon-athena-adds-support-for-querying-encrypted-data/) from the *AWS Big Data Blog*\.

### Unsupported options<a name="encryption-unsupported-options"></a>

The following encryption options are not supported:
+ SSE with customer\-provided keys \(SSE\-C\)\.
+ Client\-side encryption using a client\-side managed key\.
+ Asymmetric keys\.

To compare Amazon S3 encryption options, see [Protecting data using encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html) in the *Amazon Simple Storage Service User Guide*\.

### Tools for client\-side encryption<a name="encryption-client-side-tools"></a>

 For client\-side encryption, note that two tools are available: 
+ [Amazon S3 encryption client](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/s3/AmazonS3EncryptionClient.html) – This encrypts data for Amazon S3 only and is supported by Athena\.
+ [AWS Encryption SDK](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/introduction.html) – The SDK can be used to encrypt data anywhere across AWS but is not directly supported by Athena\.

These tools are not compatible, and data encrypted using one tool cannot be decrypted by the other\. Athena only supports the Amazon S3 Encryption Client directly\. If you use the SDK to encrypt your data, you can run queries from Athena, but the data is returned as encrypted text\. 

If you want to use Athena to query data that has been encrypted with the AWS Encryption SDK, you must download and decrypt your data, and then encrypt it again using the Amazon S3 Encryption Client\.

## Permissions to encrypted data in Amazon S3<a name="permissions-for-encrypting-and-decrypting-data"></a>

Depending on the type of encryption you use in Amazon S3, you may need to add permissions, also known as "Allow" actions, to your policies used in Athena:
+ **SSE\-S3** – If you use SSE\-S3 for encryption, Athena users require no additional permissions in their policies\. It is sufficient to have the appropriate Amazon S3 permissions for the appropriate Amazon S3 location and for Athena actions\. For more information about policies that allow appropriate Athena and Amazon S3 permissions, see [AWS managed policies for Amazon Athena](managed-policies.md) and [Access to Amazon S3](s3-permissions.md)\.
+ **AWS KMS** – If you use AWS KMS for encryption, Athena users must be allowed to perform particular AWS KMS actions in addition to Athena and Amazon S3 permissions\. You allow these actions by editing the key policy for the AWS KMS customer managed CMKs that are used to encrypt data in Amazon S3\. To add key users to the appropriate AWS KMS key policies, you can use the AWS KMS console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\. For information about how to add a user to a AWS KMS key policy, see [Allows key users to use the CMK](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-users) in the *AWS Key Management Service Developer Guide*\.
**Note**  
Advanced key policy administrators can adjust key policies\. `kms:Decrypt` is the minimum allowed action for an Athena user to work with an encrypted dataset\. To work with encrypted query results, the minimum allowed actions are `kms:GenerateDataKey` and `kms:Decrypt`\.

  When using Athena to query datasets in Amazon S3 with a large number of objects that are encrypted with AWS KMS, AWS KMS may throttle query results\. This is more likely when there are a large number of small objects\. Athena backs off retry requests, but a throttling error might still occur\. If you are working with a large number of encrypted objects and experience this issue, one option is to enable Amazon S3 bucket keys to reduce the number of calls to KMS\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket keys](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-key.html) in the *Amazon Simple Storage Service User Guide*\. Another option is to increase your service quotas for AWS KMS\. For more information, see [Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html#requests-per-second) in the *AWS Key Management Service Developer Guide*\.

For troubleshooting information about permissions when using Amazon S3 with Athena, see the [Permissions](troubleshooting-athena.md#troubleshooting-athena-permissions) section of the [Troubleshooting in Athena](troubleshooting-athena.md) topic\.

## Permissions to encrypted metadata in the AWS Glue Data Catalog<a name="glue-encryption"></a>

If you [encrypt metadata in the AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html), you must add `"kms:GenerateDataKey"`, `"kms:Decrypt"`, and `"kms:Encrypt"` actions to the policies you use for accessing Athena\. For information, see [Access from Athena to encrypted metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.