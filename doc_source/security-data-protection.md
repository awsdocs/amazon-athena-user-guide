# Data Protection in Athena<a name="security-data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in Amazon Athena\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put sensitive identifying information, such as your customers' account numbers, into free\-form fields such as a **Name** field\. This includes when you work with Athena or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into Athena or other services might get picked up for inclusion in diagnostic logs\. When you provide a URL to an external server, don't include credentials information in the URL to validate your request to that server\.

## Protecting Multiple Types of Data<a name="security-data-protection-types-of-data"></a>

Multiple types of data are involved when you use Athena to create databases and tables\. These data types include source data stored in Amazon S3, metadata for databases and tables that you create when you run queries or the AWS Glue Crawler to discover data, query results data, and query history\. This section discusses each type of data and provides guidance about protecting it\.
+ **Source data** – You store the data for databases and tables in Amazon S3, and Athena does not modify it\. For more information, see [Data Protection in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/DataDurability.html) in the *Amazon Simple Storage Service Developer Guide*\. You control access to your source data and can encrypt it in Amazon S3\. You can use Athena to [create tables based on encrypted datasets in Amazon S3](creating-tables-based-on-encrypted-datasets-in-s3.md)\.
+ **Database and table metadata \(schema\)** – Athena uses schema\-on\-read technology, which means that your table definitions are applied to your data in Amazon S3 when Athena runs queries\. Any schemas you define are automatically saved unless you explicitly delete them\. In Athena, you can modify the Data Catalog metadata using DDL statements\. You can also delete table definitions and schema without impacting the underlying data stored in Amazon S3\. 
**Note**  
The metadata for databases and tables you use in Athena is stored in the AWS Glue Data Catalog\. We highly recommmend that you [upgrade](glue-upgrade.md) to using the AWS Glue Data Catalog with Athena\. For more information about the benefits of using the AWS Glue Data Catalog, see [FAQ: Upgrading to the AWS Glue Data Catalog](glue-faq.md)\.

  You can [define fine\-grained access policies to databases and tables](fine-grained-access-to-glue-resources.md) registered in the AWS Glue Data Catalog using AWS Identity and Access Management \(IAM\)\. You can also [encrypt metadata in the AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html)\. If you encrypt the metadata, use [permissions to encrypted metadata](encryption.md#glue-encryption) for access\.
+ **Query results and query history, including saved queries** – Query results are stored in a location in Amazon S3 that you can choose to specify globally, or for each workgroup\. If not specified, Athena uses the default location in each case\. You control access to Amazon S3 buckets where you store query results and saved queries\. Additionally, you can choose to encrypt query results that you store in Amazon S3\. Your users must have the appropriate permissions to access the Amazon S3 locations and decrypt files\. For more information, see [Encrypting Query Results Stored in Amazon S3](encrypting-query-results-stored-in-s3.md) in this document\. 

  Athena retains query history for 45 days\. You can [ view query history](querying.md#queries-viewing-history) using Athena APIs, in the console, and with AWS CLI\. To store the queries for longer than 45 days, save them\. To protect access to saved queries, [use workgroups](workgroups.md) in Athena, restricting access to saved queries only to users who are authorized to view them\.

**Topics**
+ [Protecting Multiple Types of Data](#security-data-protection-types-of-data)
+ [Encryption at Rest](encryption.md)
+ [Encryption in Transit](encryption-in-transit.md)
+ [Key Management](key-management.md)
+ [Internetwork Traffic Privacy](internetwork-traffic-privacy.md)