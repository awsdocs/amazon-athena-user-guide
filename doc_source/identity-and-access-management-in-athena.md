# Identity and Access Management in Athena<a name="identity-and-access-management-in-athena"></a>

Amazon Athena uses AWS Identity and Access Management \(IAM\) policies to restrict access to Athena operations\.

To run queries in Athena, you must have the appropriate permissions for the following:
+ Athena API actions including additional actions for Athena [workgroups](workgroups.md)\.
+ Amazon S3 locations where the underlying data to query is stored\. 
+ Metadata and resources that you store in the AWS Glue Data Catalog, such as databases and tables, including additional actions for encrypted metadata\. 

If you are an administrator for other users, make sure that they have appropriate permissions associated with their user profiles\.

**Topics**
+ [Managed Policies for User Access](managed-policies.md)
+ [Access through JDBC and ODBC Connections](policy-actions.md)
+ [Access to Amazon S3](s3-permissions.md)
+ [Fine\-Grained Access to Databases and Tables](fine-grained-access-to-glue-resources.md)
+ [Access to Encrypted Metadata in the Data Catalog](access-encrypted-data-glue-data-catalog.md)
+ [Cross\-account Access](cross-account-permissions.md)
+ [Access to Workgroups and Tags](workgroups-access.md)
+ [Allow Access to an Athena Data Connector for External Hive Metastore \(Preview\)](hive-metastore-iam-access.md)
+ [Allow Access to Athena Federated Query \(Preview\)](federated-query-iam-access.md)
+ [Allow Access to Athena UDF](udf-iam-access.md)
+ [Allowing Access for ML with Athena \(Preview\)](machine-learning-iam-access.md)
+ [Enabling Federated Access to the Athena API](access-federation-saml.md)