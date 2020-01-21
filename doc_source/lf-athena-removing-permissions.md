# Applying Lake Formation Permissions to Existing Databases and Tables<a name="lf-athena-removing-permissions"></a>

If you are new to Athena and you use Lake Formation to configure access to query data, you do not need to configure IAM policies so that users can read Amazon S3 data and create metadata\. You can use Lake Formation to administer permissions\.

Registering data with Lake Formation and updating IAM permissions policies is not a requirement\. If data is not registered with Lake Formation, Athena users who have appropriate permissions in Amazon S3—and AWS Glue, if applicable—can continue to query data not registered with Lake Formation\.

If you have existing Athena users who query data not registered with Lake Formation, you can update IAM permissions for Amazon S3—and the AWS Glue Data Catalog, if applicable—so that you can use Lake Formation permissions to manage user access centrally\. For permission to read Amazon S3 data locations, you can update resource\-based and identity\-based policies to remove Amazon S3 permissions\. For access to metadata, if you configured resource\-level policies for fine\-grained access control with AWS Glue, you can use Lake Formation permissions to manage access instead\. 

For more information, see [Fine\-Grained Access to Databases and Tables in the AWS Glue Data Catalog](fine-grained-access-to-glue-resources.md) and [Upgrading AWS Glue Data Permissions to the AWS Lake Formation Model](https://docs.aws.amazon.com/lake-formation/latest/dg/upgrade-glue-lake-formation.html) in the *AWS Lake Formation Developer Guide*\.