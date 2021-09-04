# Managing Lake Formation and Athena User Permissions<a name="lf-athena-user-permissions"></a>

Lake Formation vends credentials to query Amazon S3 data stores that are registered with Lake Formation\. If you previously used IAM policies to allow or deny permissions to read data locations in Amazon S3, you can use Lake Formation permissions instead\. However, other IAM permissions are still required\.

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

The following sections summarize the permissions required to use Athena to query data registered in Lake Formation\. For more information, see [Security in AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/security.html) in the *AWS Lake Formation Developer Guide*\.

**Topics**
+ [Identity\-Based Permissions For Lake Formation and Athena](#lf-athena-user-permissions-identity-based)
+ [Amazon S3 Permissions For Athena Query Results Locations](#lf-athena-user-permissions-query-results-locations)
+ [Athena Workgroup Memberships To Query History](#lf-athena-user-permissions-workgroup-memberships-query-history)
+ [Lake Formation Permissions To Data](#lf-athena-user-permissions-data)
+ [IAM Permissions to Write to Amazon S3 Locations](#lf-athena-user-permissions-s3-write)
+ [Permissions to Encrypted Data, Metadata, and Athena Query Results](#lf-athena-user-permissions-encrypted)
+ [Resource\-Based Permissions for Amazon S3 Buckets in External Accounts \(Optional\)](#lf-athena-user-permissions-s3-cross-account)

## Identity\-Based Permissions For Lake Formation and Athena<a name="lf-athena-user-permissions-identity-based"></a>

Anyone using Athena to query data registered with Lake Formation must have an IAM permissions policy that allows the `lakeformation:GetDataAccess` action\. The [AWS managed policy: AmazonAthenaFullAccess](managed-policies.md#amazonathenafullaccess-managed-policy) allows this action\. If you use inline policies, be sure to update permissions policies to allow this action\.

In Lake Formation, a *data lake administrator* has permissions to create metadata objects such as databases and tables, grant Lake Formation permissions to other users, and register new Amazon S3 locations\. To register new locations, permissions to the service\-linked role for Lake Formation are required\. For more information, see [Create a Data Lake Administrator](https://docs.aws.amazon.com/lake-formation/latest/dg/getting-started-setup.html#create-data-lake-admin) and [Service\-Linked Role Permissions for Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/service-linked-roles.html#service-linked-role-permissions) in the *AWS Lake Formation Developer Guide*\.

An Lake Formation user can use Athena to query databases, tables, table columns, and underlying Amazon S3 data stores based on Lake Formation permissions granted to them by data lake administrators\. Users cannot create databases or tables, or register new Amazon S3 locations with Lake Formation\. For more information, see [Create a Data Lake User](https://docs.aws.amazon.com/lake-formation/latest/dg/cloudtrail-tut-create-lf-user.html) in the *AWS Lake Formation Developer Guide*\.

In Athena, identity\-based permissions policies, including those for Athena workgroups, still control access to Athena actions for Amazon Web Services account users\. In addition, federated access might be provided through the SAML\-based authentication available with Athena drivers\. For more information, see [Using Workgroups to Control Query Access and Costs](manage-queries-control-costs-with-workgroups.md), [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md), and [Enabling Federated Access to the Athena API](access-federation-saml.md)\.

For more information, see [Granting Lake Formation Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/lake-formation-permissions.html) in the *AWS Lake Formation Developer Guide*\.

## Amazon S3 Permissions For Athena Query Results Locations<a name="lf-athena-user-permissions-query-results-locations"></a>

The query results locations in Amazon S3 for Athena cannot be registered with Lake Formation\. Lake Formation permissions do not limit access to these locations\. Unless you limit access, Athena users can access query result files and metadata when they do not have Lake Formation permissions for the data\. To avoid this, we recommend that you use workgroups to specify the location for query results and align workgroup membership with Lake Formation permissions\. You can then use IAM permissions policies to limit access to query results locations\. For more information about query results, see [Working with Query Results, Output Files, and Query History](querying.md)\.

## Athena Workgroup Memberships To Query History<a name="lf-athena-user-permissions-workgroup-memberships-query-history"></a>

Athena query history exposes a list of saved queries and complete query strings\. Unless you use workgroups to separate access to query histories, Athena users who are not authorized to query data in Lake Formation are able to view query strings run on that data, including column names, selection criteria, and so on\. We recommend that you use workgroups to separate query histories, and align Athena workgroup membership with Lake Formation permissions to limit access\. For more information, see [Using Workgroups to Control Query Access and Costs](manage-queries-control-costs-with-workgroups.md)\.

## Lake Formation Permissions To Data<a name="lf-athena-user-permissions-data"></a>

In addition to the baseline permission to use Lake Formation, Athena users must have Lake Formation permissions to access resources that they query\. These permissions are granted and managed by a Lake Formation administrator\. For more information, see [Security and Access Control to Metadata and Data](https://docs.aws.amazon.com/lake-formation/latest/dg/security-data-access.html#security-data-access-permissions) in the *AWS Lake Formation Developer Guide*\.

## IAM Permissions to Write to Amazon S3 Locations<a name="lf-athena-user-permissions-s3-write"></a>

Lake Formation permissions to Amazon S3 do not include the ability to write to Amazon S3\. Create Table As Statements \(CTAS\) require write access to the Amazon S3 location of tables\. To run CTAS queries on data registered with Lake Formation, Athena users must have IAM permissions to write to the table Amazon S3 locations in addition to the appropriate Lake Formation permissions to read the data locations\. For more information, see [Creating a Table from Query Results \(CTAS\)](ctas.md)\.

## Permissions to Encrypted Data, Metadata, and Athena Query Results<a name="lf-athena-user-permissions-encrypted"></a>

Underlying source data in Amazon S3 and metadata in the Data Catalog that is registered with Lake Formation can be encrypted\. There is no change to the way that Athena handles encryption of query results when using Athena to query data registered with Lake Formation\. For more information, see [Encrypting Query Results Stored in Amazon S3](encrypting-query-results-stored-in-s3.md)\.
+ **Encrypting source data** – Encryption of Amazon S3 data locations source data is supported\. Athena users who query encrypted Amazon S3 locations that are registered with Lake Formation need permissions to encrypt and decrypt data\. For more information about requirements, see [Supported Amazon S3 Encryption Options](encryption.md#encryption-options-S3-and-Athena) and [Permissions to Encrypted Data in Amazon S3](encryption.md#permissions-for-encrypting-and-decrypting-data)\. 
+ **Encrypting metadata** – Encrypting metadata in the Data Catalog is supported\. For principals using Athena, identity\-based policies must allow the `"kms:GenerateDataKey"`, `"kms:Decrypt"`, and `"kms:Encrypt"` actions for the key used to encrypt metadata\. For more information, see [Encrypting Your Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html) in the *AWS Glue Developer Guide* and [Access to Encrypted Metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.

## Resource\-Based Permissions for Amazon S3 Buckets in External Accounts \(Optional\)<a name="lf-athena-user-permissions-s3-cross-account"></a>

To query an Amazon S3 data location in a different account, a resource\-based IAM policy \(bucket policy\) must allow access to the location\. For more information, see [Cross\-account Access in Athena to Amazon S3 Buckets](cross-account-permissions.md)\.

For information about accessing a Data Catalog in another account, see [Cross\-Account Data Catalog Access](lf-athena-limitations.md#lf-athena-limitations-cross-account-glue)\.