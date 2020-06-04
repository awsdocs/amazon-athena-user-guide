# MSCK REPAIR TABLE<a name="msck-repair-table"></a>

Use the `MSCK REPAIR TABLE` command to update the metadata in the catalog after you add or remove Hive compatible partitions\. 

The `MSCK REPAIR TABLE` command scans a file system such as Amazon S3 for Hive compatible partitions that were added to or removed from the file system after the table was created\. The command updates the metadata in the catalog regarding the partitions and the data associated with them\.

When you add or remove partitions, the metadata in the catalog becomes inconsistent with the layout of the data in the file system\. For example, after you create a table with partitions, information about the new partitions needs to be added to the catalog\. To update the metadata, you run `MSCK REPAIR TABLE` on the table\. This enables you to query the data in the new partitions from Athena\.

## Considerations and Limitations<a name="msck-repair-table-considerations"></a>

When using `MSCK REPAIR TABLE`, keep in mind the following points:
+ It is possible it will take some time to add all partitions\. If this operation times out, it will be in an incomplete state where only a few partitions are added to the catalog\. You should run `MSCK REPAIR TABLE` on the same table until all partitions are added\. For more information, see [Partitioning Data](partitions.md)\. 
+ For partitions that are not compatible with Hive, use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) to load the partitions so that you can query their data\.
+ Partition locations to be used with Athena must use the `s3` protocol \(for example, `s3://bucket/folder/`\)\. In Athena, locations that use other protocols \(for example, `s3a://bucket/folder/`\) will result in query failures when `MSCK REPAIR TABLE` queries are run on the containing tables\. 

## Synopsis<a name="synopsis"></a>

```
MSCK REPAIR TABLE table_name
```

## Examples<a name="examples"></a>

```
MSCK REPAIR TABLE orders;
```

## Troubleshooting<a name="msck-repair-table-troubleshooting"></a>

After you run MSCK REPAIR TABLE, if Athena does not add the partitions to the table in the AWS Glue Data Catalog, check the following:
+ Make sure that the AWS Identity and Access Management \(IAM\) user or role has a policy that allows the `glue:BatchCreatePartition` action\.
+ Make sure that the Amazon S3 path is in lower case instead of camel case \(for example, `userid` instead of `userId`\)\.

The following sections provide additional detail\.

### Allow glue:BatchCreatePartition in the IAM policy<a name="msck-repair-table-troubleshooting-allow-gluebatchcreatepartition-in-the-IAM-policy"></a>

Review the IAM policies attached to the user or role that you're using to execute `MSCK REPAIR TABLE`\. When you [use the AWS Glue Data Catalog with Athena](glue-athena.md), the IAM policy must allow the `glue:BatchCreatePartition` action\. For an example of an IAM policy that allows the `glue:BatchCreatePartition` action, see [AmazonAthenaFullAccess Managed Policy](amazonathenafullaccess-managed-policy.md)\.

### Change the Amazon S3 path to lower case<a name="msck-repair-table-troubleshooting-change-the-amazon-s3-path-to-flat-case"></a>

The Amazon S3 path must be in lower case\. If the S3 path is in camel case, `MSCK REPAIR TABLE` doesn't add the partitions to the AWS Glue Data Catalog\. For example, if your S3 path is `userId`, the following partitions aren't added to the AWS Glue Data Catalog:

```
s3://bucket/path/userId=1/

s3://bucket/path/userId=2/

s3://bucket/path/userId=3/
```

To resolve this issue, use flat case instead of camel case:

```
s3://bucket/path/userid=1/

s3://bucket/path/userid=2/

s3://bucket/path/userid=3/
```