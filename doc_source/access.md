# Access Control Policies<a name="access"></a>

To run queries in Athena, you must have the appropriate permissions for:
+ The Athena actions\.
+ The Amazon S3 locations where the underlying data is stored that you are going to query in Athena\. 
+ The resources that you store in AWS Glue Data Catalog, such as databases and tables, that you are going to query in Athena\. 
+ The encrypted metadata in the AWS Glue Data Catalog \(if you migrated to using that metadata in Athena and the metadata is encrypted\)\. 
+ Actions on Athena [workgroups](workgroups.md)\.

If you are an administrator for other users, make sure that they have appropriate permissions associated with their user profiles\.

**Topics**
+ [Managed Policies for User Access](managed-policies.md)
+ [Access to Amazon S3](s3-permissions.md)
+ [Fine\-Grained Access to Databases and Tables](fine-grained-access-to-glue-resources.md)
+ [Access to Encrypted Metadata in the Data Catalog](access-encrypted-data-glue-data-catalog.md)
+ [Cross\-account Access](cross-account-permissions.md)
+ [Workgroup and Tag Policies](workgroups-access.md)
+ [Enabling Federated Access to Athena API](access-federation-saml.md)