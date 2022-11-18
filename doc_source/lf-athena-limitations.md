# Considerations and limitations when using Athena to query data registered with Lake Formation<a name="lf-athena-limitations"></a>

Consider the following when using Athena to query data registered in Lake Formation\. For additional information, see [Known issues for AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/limitations.html) in the *AWS Lake Formation Developer Guide*\.

**Topics**
+ [Column metadata visible to users without data permissions to column in some circumstances](#lf-athena-limitations-column-metadata)
+ [Working with Lake Formation permissions to views](#lf-athena-limitations-permissions-to-views)
+ [Lake Formation fine\-grained access control and Athena workgroups](#lf-athena-limitations-fine-grained-access-control)
+ [Lake Formation data filter limitations](#lf-athena-limitations-data-filters)
+ [Athena query results location in Amazon S3 not registered with Lake Formation](#lf-athena-limitations-query-results-location)
+ [Use Athena workgroups to limit access to query history](#lf-athena-limitations-use-workgroups-to-limit-access-to-query-history)
+ [Cross\-account access](#lf-athena-limitations-cross-account)
+ [CSE\-KMS Amazon S3 registered with Lake Formation cannot be queried in Athena](#lf-athena-limitations-cse-kms)
+ [Partitioned data locations registered with Lake Formation must be in table subdirectories](#lf-athena-limitations-partioned-data-locations)
+ [Create table as select \(CTAS\) queries require Amazon S3 write permissions](#lf-athena-limitations-ctas-queries)
+ [The DESCRIBE permission is required on the default database](#lf-athena-limitations-describe-default)

## Column metadata visible to unauthorized users in some circumstances with Avro and custom SerDe<a name="lf-athena-limitations-column-metadata"></a>

Lake Formation column\-level authorization prevents users from accessing data in columns for which the user does not have Lake Formation permissions\. However, in certain situations, users are able to access metadata describing all columns in the table, including the columns for which they do not have permissions to the data\.

This occurs when column metadata is stored in table properties for tables using either the Apache Avro storage format or using a custom Serializer/Deserializer \(SerDe\) in which table schema is defined in table properties along with the SerDe definition\. When using Athena with Lake Formation, we recommend that you review the contents of table properties that you register with Lake Formation and, where possible, limit the information stored in table properties to prevent any sensitive metadata from being visible to users\.

## Working with Lake Formation permissions to views<a name="lf-athena-limitations-permissions-to-views"></a>

For data registered with Lake Formation, an Athena user can create a `VIEW` only if they have Lake Formation permissions to the tables, columns, and source Amazon S3 data locations on which the `VIEW` is based\. After a `VIEW` is created in Athena, Lake Formation permissions can be applied to the `VIEW`\. Column\-level permissions are not available for a `VIEW`\. Users who have Lake Formation permissions to a `VIEW` but do not have permissions to the table and columns on which the view was based are not able to use the `VIEW` to query data\. However, users with this mix of permissions are able to use statements like `DESCRIBE VIEW`, `SHOW CREATE VIEW`, and `SHOW COLUMNS` to see `VIEW` metadata\. For this reason, be sure to align Lake Formation permissions for each `VIEW` with underlying table permissions\. Cell filters defined on a table do not apply to a `VIEW` for that table\. For more information about setting up permissions for shared views across accounts, see [Cross\-account Data Catalog access](#lf-athena-limitations-cross-account)\.

## Lake Formation fine\-grained access control and Athena workgroups<a name="lf-athena-limitations-fine-grained-access-control"></a>

Users in the same Athena workgroup can see the data that Lake Formation fine\-grained access control has configured to be accessible to the workgroup\. For more information about using fine\-grained access control in Lake Formation, see [Manage fine\-grained access control using AWS Lake Formation](http://aws.amazon.com/blogs/big-data/manage-fine-grained-access-control-using-aws-lake-formation/) in the *AWS Big Data Blog*\. 

## Lake Formation data filter limitations<a name="lf-athena-limitations-data-filters"></a>

Lake Formation [data filters](https://docs.aws.amazon.com/lake-formation/latest/dg/data-filters-about.html) cannot be used with partition projection in Athena\. If you enable data filters on a database or table in Lake Formation, partition projection stops working\.

Lake Formation data filters do not support the Hive JSON SerDe\.

## Athena query results location in Amazon S3 not registered with Lake Formation<a name="lf-athena-limitations-query-results-location"></a>

The query results locations in Amazon S3 for Athena cannot be registered with Lake Formation\. Lake Formation permissions do not limit access to these locations\. Unless you limit access, Athena users can access query result files and metadata when they do not have Lake Formation permissions for the data\. To avoid this, we recommend that you use workgroups to specify the location for query results and align workgroup membership with Lake Formation permissions\. You can then use IAM permissions policies to limit access to query results locations\. For more information about query results, see [Working with query results, recent queries, and output files](querying.md)\.

## Use Athena workgroups to limit access to query history<a name="lf-athena-limitations-use-workgroups-to-limit-access-to-query-history"></a>

Athena query history exposes a list of saved queries and complete query strings\. Unless you use workgroups to separate access to query histories, Athena users who are not authorized to query data in Lake Formation are able to view query strings run on that data, including column names, selection criteria, and so on\. We recommend that you use workgroups to separate query histories, and align Athena workgroup membership with Lake Formation permissions to limit access\. For more information, see [Using workgroups to control query access and costs](manage-queries-control-costs-with-workgroups.md)\.

## Cross\-account Data Catalog access<a name="lf-athena-limitations-cross-account"></a>

To access a data catalog in another account, you can use Athena's cross\-account AWS Glue feature or set up cross\-account access in Lake Formation\.

### Athena cross\-account Data Catalog access<a name="lf-athena-limitations-cross-account-glue"></a>

You can use Athena's cross\-account AWS Glue catalog feature to register the catalog in your account\. This capability is available only in Athena engine version 2 and later versions and is limited to same\-Region use between accounts\. For more information, see [Registering an AWS Glue Data Catalog from another account](data-sources-glue-cross-account.md)\.

If the Data Catalog to be shared has a resource policy configured in AWS Glue, it must be updated to allow access to the AWS Resource Access Manager and grant permissions to Account B to use Account A's Data Catalog, as in the following example\. 

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Principal": {
            "Service": "ram.amazonaws.com"
        },
        "Action": "glue:ShareResource",
        "Resource": [
          "arn:aws:glue:<REGION>:<ACCOUNT-A>:table/*/*", 
          "arn:aws:glue:<REGION>:<ACCOUNT-A>:database/*", 
          "arn:aws:glue:<REGION>:<ACCOUNT-A>:catalog"
        ]
    }, 
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::<ACCOUNT-B>:root"
        },
        "Action": "glue:*",
        "Resource": [
          "arn:aws:glue:<REGION>:<ACCOUNT-A>:table/*/*", 
          "arn:aws:glue:<REGION>:<ACCOUNT-A>:database/*", 
          "arn:aws:glue:<REGION>:<ACCOUNT-A>:catalog"
        ]
    }
    ]
}
```

For more information, see [Cross\-account access to AWS Glue data catalogs](security-iam-cross-account-glue-catalog-access.md)\.

### Setting up cross\-account access in Lake Formation<a name="lf-athena-limitations-cross-account-glue-lf-xacct"></a>

AWS Lake Formation lets you use a single account to manage a central Data Catalog\. You can use this feature to implement [cross\-account access](https://docs.aws.amazon.com/lake-formation/latest/dg/access-control-cross-account.html) to Data Catalog metadata and underlying data\. For example, an owner account can grant another \(recipient\) account `SELECT` permission on a table\. 

For a shared database or table to appear in the Athena Query Editor, you [create a resource link](https://docs.aws.amazon.com/lake-formation/latest/dg/resource-links-about.html) in Lake Formation to the shared database or table\. When the recipient account in Lake Formation queries the owner's table, [CloudTrail](https://docs.aws.amazon.com/lake-formation/latest/dg/cross-account-logging.html) adds the data access event to the logs for both the recipient account and the owner account\.

For shared views, keep in mind the following points:
+ Queries are run on target resource links, not on the source table or view, and then the output is shared to the target account\.
+ It is not sufficient to share only the view\. All the tables that are involved in creating the view must be part of the cross\-account share\.
+ The name of the resource link created on the shared resources must match the name of the resource in the owner account\. If the name does not match, an error message like Failed analyzing stored view 'awsdatacatalog\.*my\-lf\-resource\-link*\.*my\-lf\-view*': line 3:3: Schema *schema\_name* does not exist occurs\.

For more information about cross\-account access in Lake Formation, see the following resources in the *AWS Lake Formation Developer Guide*:

 [Cross\-account access](https://docs.aws.amazon.com/lake-formation/latest/dg/access-control-cross-account.html) 

 [How resource links work in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/resource-links-about.html) 

 [Cross\-account CloudTrail logging](https://docs.aws.amazon.com/lake-formation/latest/dg/cross-account-logging.html) 

## CSE\-KMS encrypted Amazon S3 locations registered with Lake Formation cannot be queried in Athena<a name="lf-athena-limitations-cse-kms"></a>

Amazon S3 data locations that are registered with Lake Formation and encrypted using client\-side encryption \(CSE\) with AWS KMS customer\-managed keys \(CSE\-KMS\) cannot be queried using Athena\. You still can use Athena to query CSE\-KMS encrypted Amazon S3 data locations that are not registered with Lake Formation and use IAM policies to allow or deny access\.

## Partitioned data locations registered with Lake Formation must be in table subdirectories<a name="lf-athena-limitations-partioned-data-locations"></a>

Partitioned tables registered with Lake Formation must have partitioned data in directories that are subdirectories of the table in Amazon S3\. For example, a table with the location `s3://mydata/mytable` and partitions `s3://mydata/mytable/dt=2019-07-11`, `s3://mydata/mytable/dt=2019-07-12`, and so on can be registered with Lake Formation and queried using Athena\. On the other hand, a table with the location `s3://mydata/mytable` and partitions located in `s3://mydata/dt=2019-07-11`, `s3://mydata/dt=2019-07-12`, and so on, cannot be registered with Lake Formation\. Because such partitions are not subdirectories of `s3://mydata/mytable`, they also cannot be read from Athena\.

## Create table as select \(CTAS\) queries require Amazon S3 write permissions<a name="lf-athena-limitations-ctas-queries"></a>

Create Table As Statements \(CTAS\) require write access to the Amazon S3 location of tables\. To run CTAS queries on data registered with Lake Formation, Athena users must have IAM permissions to write to the table Amazon S3 locations in addition to the appropriate Lake Formation permissions to read the data locations\. For more information, see [Creating a table from query results \(CTAS\)](ctas.md)\.

## The DESCRIBE permission is required on the default database<a name="lf-athena-limitations-describe-default"></a>

The Lake Formation [DESCRIBE](https://docs.aws.amazon.com/lake-formation/latest/dg/lf-permissions-reference.html#perm-describe) permission is required on the `default` database\. The following example AWS CLI command grants the `DESCRIBE` permission on the `default` database to the user `datalake_user1` in AWS account `111122223333`\.

```
aws lakeformation grant-permissions --principal DataLakePrincipalIdentifier=arn:aws:iam::111122223333:user/datalake_user1 --permissions "DESCRIBE" --resource '{ "Database": {"Name":"default"}}
```

For more information, see the [Lake Formation permissions reference](https://docs.aws.amazon.com/lake-formation/latest/dg/lf-permissions-reference.html) in the *AWS Lake Formation Developer Guide*\.