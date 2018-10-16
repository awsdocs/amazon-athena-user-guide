# October 15, 2018<a name="release-note-2018-10-15"></a>

Published on *2018\-10\-15*

If you have upgraded to the AWS Glue Data Catalog, there are two new features that provide support for: 
+ Encryption of the Data Catalog metadata\. If you choose to encrypt metadata in the Data Catalog, you must add specific policies to Athena\. For more information, see [Access to Encrypted Metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.
+ Fine\-grained permissions to access resources in the AWS Glue Data Catalog\. You can now define identity\-based \(IAM\) policies that restrict or allow access to specific databases and tables from the Data Catalog used in Athena\. For more information, see [Fine\-Grained Access to Databases and Tables in the AWS Glue Data Catalog](fine-grained-access-to-glue-resources.md)\.
**Note**  
Data resides in the Amazon S3 buckets, and access to it is governed by the [Amazon S3 Permissions](s3-permissions.md)\. To access data in databases and tables, continue to use access control policies to Amazon S3 buckets that store the data\. 