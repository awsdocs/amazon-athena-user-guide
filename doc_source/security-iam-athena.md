# Identity and Access Management in Athena<a name="security-iam-athena"></a>

Amazon Athena uses [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) policies to restrict access to Athena operations\. For a full list of permissions for Athena, see [Actions, Resources, and Condition Keys for Amazon Athena](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonathena.html) in the **\. 

The permissions required to run Athena queries include the following:
+ Amazon S3 locations where the underlying data to query is stored\. For more information, see [Identity and access management in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html) in the *Amazon Simple Storage Service Developer Guide*\.
+ Metadata and resources that you store in the AWS Glue Data Catalog, such as databases and tables, including additional actions for encrypted metadata\. For more information, see [Setting up IAM Permissions for AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/getting-started-access.html) and [Setting Up Encryption in AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/set-up-encryption.html) in the *AWS Glue Developer Guide*\.
+ Athena API actions\. For a list of API actions in Athena, see [Actions](https://docs.aws.amazon.com/athena/latest/APIReference/API_Operations.html) in the *Amazon Athena API Reference*\.

The following topics provide more information about permissions for specific areas of Athena\.

**Topics**
+ [Managed Policies for User Access](managed-policies.md)
+ [Access through JDBC and ODBC Connections](policy-actions.md)
+ [Access to Amazon S3](s3-permissions.md)
+ [Fine\-Grained Access to Databases and Tables](fine-grained-access-to-glue-resources.md)
+ [Access to Encrypted Metadata in the Data Catalog](access-encrypted-data-glue-data-catalog.md)
+ [Cross\-account Access to S3 Buckets](cross-account-permissions.md)
+ [Access to Workgroups and Tags](workgroups-access.md)
+ [Using CalledVia Context Keys](security-iam-athena-calledvia.md)
+ [Allow Access to an Athena Data Connector for External Hive Metastore](hive-metastore-iam-access.md)
+ [Allow Lambda Function Access to External Hive Metastores](hive-metastore-iam-access-lambda.md)
+ [Allow Access to Athena Federated Query](federated-query-iam-access.md)
+ [Allow Access to Athena UDF](udf-iam-access.md)
+ [Allowing Access for ML with Athena \(Preview\)](machine-learning-iam-access.md)
+ [Enabling Federated Access to the Athena API](access-federation-saml.md)