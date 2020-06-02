# June 1, 2020<a name="release-note-2020-06-01"></a>

Published on 2020\-06\-01

## Using Apache Hive Metastore as a Metacatalog with Amazon Athena<a name="release-note-2020-06-01-hive-metastore"></a>

You can now connect Athena to one or more Apache Hive metastores in addition to the AWS Glue Data Catalog with Athena\.

To connect to a self\-hosted Hive metastore, you need an Athena Hive metastore connector\. Athena provides a [reference implementation](datastores-hive-reference-implementation.md) connector that you can use\. The connector runs as an AWS Lambda function in your account\. 

For more information, see [Using Athena Data Connector for External Hive Metastore](connect-to-data-source-hive.md)\.