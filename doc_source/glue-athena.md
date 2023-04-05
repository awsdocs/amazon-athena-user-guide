# Integration with AWS Glue<a name="glue-athena"></a>

 [AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) is a fully managed ETL \(extract, transform, and load\) AWS service\. One of its key abilities is to analyze and categorize data\. You can use AWS Glue crawlers to automatically infer database and table schema from your data in Amazon S3 and store the associated metadata in the AWS Glue Data Catalog\. 

Athena uses the AWS Glue Data Catalog to store and retrieve table metadata for the Amazon S3 data in your Amazon Web Services account\. The table metadata lets the Athena query engine know how to find, read, and process the data that you want to query\. 

To create database and table schema in the AWS Glue Data Catalog, you can run an AWS Glue crawler from within Athena on a data source, or you can run Data Definition Language \(DDL\) queries directly in the Athena Query Editor\. Then, using the database and table schema that you created, you can use Data Manipulation \(DML\) queries in Athena to query the data\.

You can register an AWS Glue Data Catalog from an account other than your own\. After you configure the required IAM permissions for AWS Glue, you can use Athena to run cross\-account queries\. For more information, see [Cross\-account access to AWS Glue data catalogs](security-iam-cross-account-glue-catalog-access.md)\.

For more information about the AWS Glue Data Catalog, see [Data Catalog and crawlers in AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/catalog-and-crawler.html) in the *AWS Glue Developer Guide*\.

Separate charges apply to AWS Glue\. For more information, see [AWS Glue pricing](https://aws.amazon.com/glue/pricing) and [Are there separate charges for AWS Glue?](glue-faq.md#faq-pricing) For more information about the benefits of using AWS Glue with Athena, see [Why should I upgrade to the AWS Glue Data Catalog?](glue-faq.md#faq-benefits) 

**Topics**
+ [Using AWS Glue to connect to data sources in Amazon S3](data-sources-glue.md)
+ [Registering an AWS Glue Data Catalog from another account](data-sources-glue-cross-account.md)
+ [Best practices when using Athena with AWS Glue](glue-best-practices.md)
+ [Recreating a database](glue-recreate-db-and-tables-cli.md)
+ [Upgrading to the AWS Glue Data Catalog step\-by\-step](glue-upgrade.md)
+ [FAQ: Upgrading to the AWS Glue Data Catalog](glue-faq.md)