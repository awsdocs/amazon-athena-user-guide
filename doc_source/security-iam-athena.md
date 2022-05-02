# Identity and access management in Athena<a name="security-iam-athena"></a>

Amazon Athena uses [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) policies to restrict access to Athena operations\. For a full list of permissions for Athena, see [Actions, resources, and condition keys for Amazon Athena](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonathena.html) in the *Service Authorization Reference*\.

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

The permissions required to run Athena queries include the following:
+ Amazon S3 locations where the underlying data to query is stored\. For more information, see [Identity and access management in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html) in the *Amazon Simple Storage Service User Guide*\.
+ Metadata and resources that you store in the AWS Glue Data Catalog, such as databases and tables, including additional actions for encrypted metadata\. For more information, see [Setting up IAM permissions for AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/getting-started-access.html) and [Setting up encryption in AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/set-up-encryption.html) in the *AWS Glue Developer Guide*\.
+ Athena API actions\. For a list of API actions in Athena, see [Actions](https://docs.aws.amazon.com/athena/latest/APIReference/API_Operations.html) in the *Amazon Athena API Reference*\.

The following topics provide more information about permissions for specific areas of Athena\.

**Topics**
+ [AWS managed policies](managed-policies.md)
+ [Access through JDBC and ODBC connections](policy-actions.md)
+ [Access to Amazon S3](s3-permissions.md)
+ [Cross\-account access to S3 buckets](cross-account-permissions.md)
+ [Fine\-grained access to databases and tables in AWS Glue](fine-grained-access-to-glue-resources.md)
+ [Cross\-account access to AWS Glue data catalogs](security-iam-cross-account-glue-catalog-access.md)
+ [Access to encrypted metadata in the Data Catalog](access-encrypted-data-glue-data-catalog.md)
+ [Access to workgroups and tags](workgroups-access.md)
+ [Allow access to prepared statements](security-iam-athena-prepared-statements.md)
+ [Using CalledVia context keys](security-iam-athena-calledvia.md)
+ [Allow access to an Athena Data Connector for External Hive Metastore](hive-metastore-iam-access.md)
+ [Allow Lambda function access to external Hive metastores](hive-metastore-iam-access-lambda.md)
+ [Allow access to Athena Federated Query](federated-query-iam-access.md)
+ [Allow access to Athena UDF](udf-iam-access.md)
+ [Allowing access for ML with Athena](machine-learning-iam-access.md)
+ [Enabling federated access to the Athena API](access-federation-saml.md)