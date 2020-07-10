# July 9, 2020<a name="release-note-2020-07-09"></a>

Published on 2020\-07\-09

Amazon Athena adds support for querying compacted Hudi datasets and adds the AWS CloudFormation `AWS::Athena::DataCatalog` resource for creating, updating, or deleting data catalogs that you register in Athena\.

## Querying Apache Hudi Datasets<a name="release-note-2020-07-09-apache-hudi"></a>

Apache Hudi is an open\-source data management framework that simplifies incremental data processing\. Amazon Athena now supports querying the read\-optimized view of an Apache Hudi dataset in your Amazon S3\-based data lake\.

For more information, see [Using Athena to Query Apache Hudi Datasets](querying-hudi.md)\.

## AWS CloudFormation Data Catalog Resource<a name="release-note-2020-07-09-data-catalog-cft"></a>

To use Amazon Athena's [federated query feature](https://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source.html) to query any data source, you must first register your data catalog in Athena\. You can now use the AWS CloudFormation `AWS::Athena::DataCatalog` resource to create, update, or delete data catalogs that you register in Athena\.

For more information, see [AWS::Athena::DataCatalog](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-datacatalog.html) in the *AWS CloudFormation User Guide*\.