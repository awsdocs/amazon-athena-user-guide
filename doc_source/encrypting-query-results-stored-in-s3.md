# Encrypting Athena query results stored in Amazon S3<a name="encrypting-query-results-stored-in-s3"></a>

You set up query result encryption using the Athena console or when using JDBC or ODBC\. Workgroups allow you to enforce the encryption of query results\.

In the console, you can configure the setting for encryption of query results in two ways:
+ **Client\-side settings** – When you use **Settings** in the console or the API operations to indicate that you want to encrypt query results, this is known as using client\-side settings\. Client\-side settings include query results location and encryption\. If you specify them, they are used, unless they are overridden by the workgroup settings\. 
+ **Workgroup settings** – When you [create or edit a workgroup](workgroups-create-update-delete.md#creating-workgroups) and select the **Override client\-side settings** field, then all queries that run in this workgroup use the workgroup encryption and query results location settings\. For more information, see [Workgroup Settings Override Client\-Side Settings](workgroups-settings-override.md)\. 

**To encrypt query results stored in Amazon S3 using the console**
**Important**  
If your workgroup has the **Override client\-side settings** field selected, then all queries in the workgroup use the workgroup settings\. The encryption configuration and the query results location specified on the **Settings** tab in the Athena console, by API operations and by JDBC and ODBC drivers are not used\. For more information, see [Workgroup Settings Override Client\-Side Settings](workgroups-settings-override.md)\.

1. In the Athena console, choose **Settings**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/settings.png)

1. Choose **Manage**\.

1. For **Location of query result**, enter or choose an Amazon S3 path\. This is the Amazon S3 location where query results are stored\.

1. Choose **Encrypt query results**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypt-query-results.png)

1. For **Encryption type**, choose **CSE\-KMS**, **SSE\-KMS**, or **SSE\-S3**\.

1. If you chose **SSE\-KMS** or **CSE\-KMS**, specify an AWS KMS key\.
   + For **Choose an AWS KMS key**, if your account has access to an existing AWS KMS customer managed key \(CMK\), choose its alias or enter an AWS KMS key ARN\.
   +  If your account does not have access to an existing customer managed key \(CMK\), choose **Create an AWS KMS key**, and then open the [AWS KMS console](https://console.aws.amazon.com/kms)\. For more information, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Note**  
Athena supports only symmetric keys for reading and writing data\.

1. Return to the Athena console and choose the key that you created by alias or ARN\. 

1. Choose **Save**\.

## Encrypting Athena query results when using JDBC or ODBC<a name="encrypting-query-results-stored-in-s3-jdbc-odbc"></a>

If you connect using the JDBC or ODBC driver, you configure driver options to specify the type of encryption to use and the Amazon S3 staging directory location\. To configure the JDBC or ODBC driver to encrypt your query results using any of the encryption protocols that Athena supports, see [Connecting to Amazon Athena with ODBC and JDBC drivers](athena-bi-tools-jdbc-odbc.md)\.