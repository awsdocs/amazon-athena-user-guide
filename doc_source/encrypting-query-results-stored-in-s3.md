# Encrypting Athena Query Results Stored in Amazon S3<a name="encrypting-query-results-stored-in-s3"></a>

You set up query result encryption using the Athena console or when using JDBC or ODBC\. Workgroups allow you to enforce the encryption of query results\.

In the console, you can configure the setting for encryption of query results in two ways:
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
   +  If your account does not have access to an existing customer managed key \(CMK\), choose **Create KMS key**, and then open the [AWS KMS console](https://console.aws.amazon.com/kms)\. In the navigation pane, choose **AWS managed keys**\. For more information, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Note**  
Athena supports only symmetric keys for reading and writing data\.

1. Return to the Athena console to specify the key by alias or ARN as described in the previous step\. 

1. Choose **Save**\.

## Encrypting Athena query results when using JDBC or ODBC<a name="encrypting-query-results-stored-in-s3-jdbc-odbc"></a>

If you connect using the JDBC or ODBC driver, you configure driver options to specify the type of encryption to use and the Amazon S3 staging directory location\. To configure the JDBC or ODBC driver to encrypt your query results using any of the encryption protocols that Athena supports, see [Connecting to Amazon Athena with ODBC and JDBC Drivers](athena-bi-tools-jdbc-odbc.md)\.