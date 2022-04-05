# Considerations and Limitations When Using Athena to Query Data Registered With Lake Formation<a name="lf-athena-limitations"></a>

Consider the following when using Athena to query data registered in Lake Formation\. For additional information, see [Known Issues for AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/limitations.html) in the *AWS Lake Formation Developer Guide*\.

**Topics**
+ [Column Metadata Visible To Users Without Data Permissions To Column In Some Circumstances](#lf-athena-limitations-column-metadata)
+ [Working With Lake Formation Permissions To Views](#lf-athena-limitations-permissions-to-views)
+ [Athena Query Results Location In Amazon S3 Not Registered With Lake Formation](#lf-athena-limitations-query-results-location)
+ [Use Athena Workgroups To Limit Access To Query History](#lf-athena-limitations-use-workgroups-to-limit-access-to-query-history)
+ [Cross\-Account Data Catalog Access](#lf-athena-limitations-cross-account-glue)
+ [CSE\-KMS Amazon S3 Registered With Lake Formation Cannot Be Queried in Athena](#lf-athena-limitations-cse-kms)
+ [Partitioned Data Locations Registered with Lake Formation Must Be in Table Subdirectories](#lf-athena-limitations-partioned-data-locations)
+ [Create Table As Select \(CTAS\) Queries Require Amazon S3 Write Permissions](#lf-athena-limitations-ctas-queries)
+ [The DESCRIBE Permission is Required on the Default Database](#lf-athena-limitations-describe-default)

## Column Metadata Visible To Unauthorized Users In Some Circumstances With Avro and Custom SerDe<a name="lf-athena-limitations-column-metadata"></a>

Lake Formation column\-level authorization prevents users from accessing data in columns for which the user does not have Lake Formation permissions\. However, in certain situations, users are able to access metadata describing all columns in the table, including the columns for which they do not have permissions to the data\.

This occurs when column metadata is stored in table properties for tables using either the Avro storage format or using a custom Serializer/Deserializer \(SerDe\) in which table schema is defined in table properties along with the SerDe definition\. When using Athena with Lake Formation, we recommend that you review the contents of table properties that you register with Lake Formation and, where possible, limit the information stored in table properties to prevent any sensitive metadata from being visible to users\.

## Working With Lake Formation Permissions To Views<a name="lf-athena-limitations-permissions-to-views"></a>

For data registered with Lake Formation, an Athena user can create a `VIEW` only if they have Lake Formation permissions to the tables, columns, and source Amazon S3 data locations on which the `VIEW` is based\. After a `VIEW` is created in Athena, Lake Formation permissions can be applied to the `VIEW`\. Column\-level permissions are not available for a `VIEW`\. Users who have Lake Formation permissions to a `VIEW` but do not have permissions to the table and columns on which the view was based are not able to use the `VIEW` to query data\. However, users with this mix of permissions are able to use statements like `DESCRIBE VIEW`, `SHOW CREATE VIEW`, and `SHOW COLUMNS` to see `VIEW` metadata\. For this reason, be sure to align Lake Formation permissions for each `VIEW` with underlying table permissions\. Cell filters defined on a table do not apply to a `VIEW` for that table\.

## Athena Query Results Location In Amazon S3 Not Registered With Lake Formation<a name="lf-athena-limitations-query-results-location"></a>

The query results locations in Amazon S3 for Athena cannot be registered with Lake Formation\. Lake Formation permissions do not limit access to these locations\. Unless you limit access, Athena users can access query result files and metadata when they do not have Lake Formation permissions for the data\. To avoid this, we recommend that you use workgroups to specify the location for query results and align workgroup membership with Lake Formation permissions\. You can then use IAM permissions policies to limit access to query results locations\. For more information about query results, see [Working with Query Results, Recent Queries, and Output Files](querying.md)\.

## Use Athena Workgroups To Limit Access To Query History<a name="lf-athena-limitations-use-workgroups-to-limit-access-to-query-history"></a>

Athena query history exposes a list of saved queries and complete query strings\. Unless you use workgroups to separate access to query histories, Athena users who are not authorized to query data in Lake Formation are able to view query strings run on that data, including column names, selection criteria, and so on\. We recommend that you use workgroups to separate query histories, and align Athena workgroup membership with Lake Formation permissions to limit access\. For more information, see [Using Workgroups to Control Query Access and Costs](manage-queries-control-costs-with-workgroups.md)\.

## Cross\-Account Data Catalog Access<a name="lf-athena-limitations-cross-account-glue"></a>

To access a data catalog in another account, you can use one of the following methods:
+ Use Athena's cross\-account AWS Glue catalog feature to register the catalog in your account\. This capability is available only in Athena engine version 2 and is limited to same\-Region use between accounts\. For more information, see [Registering an AWS Glue Data Catalog from Another Account](data-sources-glue-cross-account.md) and [Cross\-Account Access to AWS Glue Data Catalogs](security-iam-cross-account-glue-catalog-access.md)\.
+ Set up cross\-account access in Lake Formation\. For more information, see the following section\.

### Setting Up Cross\-Account Access in Lake Formation<a name="lf-athena-limitations-cross-account-glue-lf-xacct"></a>

AWS Lake Formation lets you use a single account to manage a central Data Catalog\. You can use this feature to implement [cross\-account access](https://docs.aws.amazon.com/lake-formation/latest/dg/access-control-cross-account.html) to Data Catalog metadata and underlying data\. For example, an owner account can grant another \(recipient\) account `SELECT` permission on a table\. For a shared database or table to appear in the Athena Query Editor, you [create a resource link](https://docs.aws.amazon.com/lake-formation/latest/dg/resource-links-about.html) in Lake Formation to the shared database or table\. For a shared view, the name of the resource link must match the name of the resource in the owner account\. When the recipient account in Lake Formation queries the owner's table, [CloudTrail](https://docs.aws.amazon.com/lake-formation/latest/dg/cross-account-logging.html) adds the data access event to the logs for both the recipient account and the owner account\.

For more information, see the following resources in the AWS Lake Formation Developer Guide:

 [Cross\-Account Access](https://docs.aws.amazon.com/lake-formation/latest/dg/access-control-cross-account.html) 

 [How Resource Links Work in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/resource-links-about.html) 

 [Cross\-Account CloudTrail Logging](https://docs.aws.amazon.com/lake-formation/latest/dg/cross-account-logging.html) 

## CSE\-KMS Encrypted Amazon S3 Locations Registered With Lake Formation Cannot Be Queried in Athena<a name="lf-athena-limitations-cse-kms"></a>

Amazon S3 data locations that are registered with Lake Formation and encrypted using client\-side encryption \(CSE\) with AWS KMS customer\-managed keys \(CSE\-KMS\) cannot be queried using Athena\. You still can use Athena to query CSE\-KMS encrypted Amazon S3 data locations that are not registered with Lake Formation and use IAM policies to allow or deny access\.

## Partitioned Data Locations Registered with Lake Formation Must Be in Table Subdirectories<a name="lf-athena-limitations-partioned-data-locations"></a>

Partitioned tables registered with Lake Formation must have partitioned data in directories that are subdirectories of the table in Amazon S3\. For example, a table with the location `s3://mydata/mytable` and partitions `s3://mydata/mytable/dt=2019-07-11`, `s3://mydata/mytable/dt=2019-07-12`, and so on can be registered with Lake Formation and queried using Athena\. On the other hand, a table with the location `s3://mydata/mytable` and partitions located in `s3://mydata/dt=2019-07-11`, `s3://mydata/dt=2019-07-12`, and so on, cannot be registered with Lake Formation\. Because such partitions are not subdirectories of `s3://mydata/mytable`, they also cannot be read from Athena\.

## Create Table As Select \(CTAS\) Queries Require Amazon S3 Write Permissions<a name="lf-athena-limitations-ctas-queries"></a>

Create Table As Statements \(CTAS\) require write access to the Amazon S3 location of tables\. To run CTAS queries on data registered with Lake Formation, Athena users must have IAM permissions to write to the table Amazon S3 locations in addition to the appropriate Lake Formation permissions to read the data locations\. For more information, see [Creating a Table from Query Results \(CTAS\)](ctas.md)\.

## The DESCRIBE Permission is Required on the Default Database<a name="lf-athena-limitations-describe-default"></a>

The Lake Formation [DESCRIBE](https://docs.aws.amazon.com/lake-formation/latest/dg/lf-permissions-reference.html#perm-describe) permission is required on the `default` database\. The following example AWS CLI command grants the `DESCRIBE` permission on the `default` database to the user `datalake_user1` in AWS account `111122223333`\.

```
aws lakeformation grant-permissions --principal DataLakePrincipalIdentifier=arn:aws:iam::111122223333:user/datalake_user1 --permissions "DESCRIBE" --resource '{ "Database": {"Name":"default"}}
```

For more information, see the [Lake Formation Permissions Reference](https://docs.aws.amazon.com/lake-formation/latest/dg/lf-permissions-reference.html) in the *AWS Lake Formation Developer Guide*\.