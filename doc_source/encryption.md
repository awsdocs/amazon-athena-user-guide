# Encryption at Rest<a name="encryption"></a>

You can run queries in Amazon Athena on encrypted data in Amazon S3 in the same Region\. You can also encrypt the query results in Amazon S3 and the data in the AWS Glue Data Catalog\.

You can encrypt the following assets in Athena:
+ The results of all queries in Amazon S3, which Athena stores in a location known as the Amazon S3 results location\. You can encrypt query results stored in Amazon S3 whether the underlying dataset is encrypted in Amazon S3 or not\. For information, see [Permissions to Encrypted Query Results Stored in Amazon S3](#encrypting-query-results-stored-in-s3)\.
+ The data in the AWS Glue Data Catalog\. For information, see [Permissions to Encrypted Metadata in the AWS Glue Data Catalog](#glue-encryption)\.

## Supported Amazon S3 Encryption Options<a name="encryption-options-S3-and-Athena"></a>

Athena supports the following Amazon S3 encryption options, both for encrypted datasets in Amazon S3 in the same Region and for encrypted query results:
+ Server side encryption \(SSE\) with an Amazon S3\-managed key \([SSE\-S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html)\)
+ Server\-side encryption \(SSE\) with a AWS Key Management Service customer managed key \([SSE\-KMS](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html)\)\.
+ Client\-side encryption \(CSE\) with a AWS KMS customer managed key \([CSE\-KMS](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingClientSideEncryption.html#client-side-encryption-kms-managed-master-key-intro)\)

**Note**  
With SSE\-KMS, Athena does not require you to indicate that data is encrypted when creating a table\.

For more information about AWS KMS encryption with Amazon S3, see [What is AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) and [How Amazon Simple Storage Service \(Amazon S3\) Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-s3.html) in the *AWS Key Management Service Developer Guide*\.

Athena does not support SSE with customer\-provided keys \(SSE\-C\), nor does it support client\-side encryption using a client\-side master key\. Athena does not support asymmetric keys\. To compare Amazon S3 encryption options, see [Protecting Data Using Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html) in the *Amazon Simple Storage Service Developer Guide*\.

**Important**  
The setup for querying an encrypted dataset in Amazon S3 and the options in Athena to encrypt query results are independent\. Each option is enabled and configured separately\. You can use different encryption methods or keys for each\. This means that reading encrypted data in Amazon S3 doesn't automatically encrypt Athena query results in Amazon S3\. The opposite is also true\. Encrypting Athena query results in Amazon S3 doesn't encrypt the underlying dataset in Amazon S3\.

Regardless of whether you use options for encrypting data at rest in Amazon S3, transport layer security \(TLS\) encrypts objects in\-transit between Athena resources and between Athena and Amazon S3\. Query results that stream to JDBC or ODBC clients are encrypted using TLS\.

## Permissions to Encrypted Data in Amazon S3<a name="permissions-for-encrypting-and-decrypting-data"></a>

Depending on the type of encryption you use in Amazon S3, you may need to add permissions, also known as "Allow" actions, to your policies used in Athena:
+ **SSE\-S3** – If you use SSE\-S3 for encryption, Athena users require no additional permissions in their policies\. It is sufficient to have the appropriate Amazon S3 permissions for the appropriate Amazon S3 location and for Athena actions\. For more information about policies that allow appropriate Athena and Amazon S3 permissions, see [IAM Policies for User Access](managed-policies.md) and [Amazon S3 Permissions](s3-permissions.md)\.
+ **AWS KMS** – If you use AWS KMS for encryption, Athena users must be allowed to perform particular AWS KMS actions in addition to Athena and Amazon S3 permissions\. You allow these actions by editing the key policy for the AWS KMS customer managed keys \(CMKs\) that are used to encrypt data in Amazon S3\. The easiest way to do this is to use the IAM console to add key users to the appropriate AWS KMS key policies\. For information about how to add a user to a AWS KMS key policy, see [How to Modify a Key Policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying.html#key-policy-modifying-how-to-console-default-view) in the *AWS Key Management Service Developer Guide*\.
**Note**  
Advanced key policy administrators can adjust key policies\. `kms:Decrypt` is the minimum allowed action for an Athena user to work with an encrypted dataset\. To work with encrypted query results, the minimum allowed actions are `kms:GenerateDataKey` and `kms:Decrypt`\.

  When using Athena to query datasets in Amazon S3 with a large number of objects that are encrypted with AWS KMS, AWS KMS may throttle query results\. This is more likely when there are a large number of small objects\. Athena backs off retry requests, but a throttling error might still occur\. In this case, you can increase your service quotas for AWS KMS\. For more information, see [Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html#requests-per-second) in the *AWS Key Management Service Developer Guide*\.

## Encrypting Query Results Stored in Amazon S3<a name="encrypting-query-results-stored-in-s3"></a>

You set up query result encryption using the Athena console\. Workgroups allow you to enforce the encryption of query results\.

If you connect using the JDBC or ODBC driver, you configure driver options to specify the type of encryption to use and the Amazon S3 staging directory location\. To configure the JDBC or ODBC driver to encrypt your query results using any of the encryption protocols that Athena supports, see [Connecting to Amazon Athena with ODBC and JDBC Drivers](athena-bi-tools-jdbc-odbc.md)\.

You can configure the setting for encryption of query results in two ways:
+ **Client\-side settings** – When you use **Settings** in the console or the API operations to indicate that you want to encrypt query results, this is known as using client\-side settings\. Client\-side settings include query results location and encryption\. If you specify them, they are used, unless they are overridden by the workgroup settings\. 
+ **Workgroup settings** – When you [create or edit a workgroup](workgroups-create-update-delete.md#creating-workgroups) and select the **Override client\-side settings** field, then all queries that run in this workgroup use the workgroup settings\. For more information, see [Workgroup Settings Override Client\-Side Settings](workgroups-settings-override.md)\. Workgroup settings include query results location and encryption\. 

**To encrypt query results stored in Amazon S3 using the console**
**Important**  
If your workgroup has the **Override client\-side settings** field selected, then the queries use the workgroup settings\. The encryption configuration and the query results location listed in **Settings**, the API operations, and the drivers are not used\. For more information, see [Workgroup Settings Override Client\-Side Settings](workgroups-settings-override.md)\.

1. In the Athena console, choose **Settings**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/settings.png)

1. For **Query result location**, enter a custom value or leave the default\. This is the Amazon S3 staging directory where query results are stored\.

1. Choose **Encrypt query results**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypt_query_results.png)

1. For **Encryption type**, choose **CSE\-KMS**, **SSE\-KMS**, or **SSE\-S3**\.

1. If you chose **SSE\-KMS** or **CSE\-KMS**, specify the **Encryption key**\.
   + If your account has access to an existing AWS KMS customer managed key \(CMK\), choose its alias or choose **Enter a KMS key ARN** and then enter an ARN\.
   +  If your account does not have access to an existing AWS KMS customer managed key \(CMK\), choose **Create KMS key**, and then open the [AWS KMS console](https://console.aws.amazon.com/kms)\. In the navigation pane, choose **AWS managed keys**\. For more information, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Note**  
Athena supports only symmetric keys for reading and writing data\.

1. Return to the Athena console to specify the key by alias or ARN as described in the previous step\. 

1. Choose **Save**\.

## Creating Tables Based on Encrypted Datasets in Amazon S3<a name="creating-tables-based-on-encrypted-datasets-in-s3"></a>

When you create a table, indicate to Athena that a dataset is encrypted in Amazon S3\. This is not required when using SSE\-KMS\. For both SSE\-S3 and AWS KMS encryption, Athena determines the proper materials to use to decrypt the dataset and create the table, so you don't need to provide key information\.

Users that run queries, including the user who creates the table, must have the appropriate permissions as described earlier in this topic\.

**Important**  
If you use Amazon EMR along with EMRFS to upload encrypted Parquet files, you must disable multipart uploads by setting `fs.s3n.multipart.uploads.enabled` to `false`\. If you don't do this, Athena is unable to determine the Parquet file length and a **HIVE\_CANNOT\_OPEN\_SPLIT** error occurs\. For more information, see [Configure Multipart Upload for Amazon S3](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-upload-s3.html#Config_Multipart) in the *Amazon EMR Management Guide*\.

Indicate that the dataset is encrypted in Amazon S3 in one of the following ways\. This step is not required if SSE\-KMS is used\.
+ Use the [CREATE TABLE](create-table.md) statement with a `TBLPROPERTIES` clause that specifies `'has_encrypted_data'='true'`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypt_has_encrypted.png)
+ Use the [JDBC driver](connect-with-jdbc.md) and set the `TBLPROPERTIES` value as shown in the previous example, when you execute [CREATE TABLE](create-table.md) using `statement.executeQuery()`\. 
+ Use the **Add table** wizard in the Athena console, and then choose **Encrypted data set** when you specify a value for **Location of input data set**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypt_has_encrypted_console.png)

Tables based on encrypted data in Amazon S3 appear in the **Database** list with an encryption icon\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypted_table_icon.png)

## Permissions to Encrypted Metadata in the AWS Glue Data Catalog<a name="glue-encryption"></a>

If you [encrypt metadata in the AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html), you must add `"kms:GenerateDataKey"`, `"kms:Decrypt"`, and `"kms:Encrypt"` actions to the policies you use for accessing Athena\. For information, see [Access to Encrypted Metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.