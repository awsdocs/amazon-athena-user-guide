# MSCK REPAIR TABLE<a name="msck-repair-table"></a>

Use the `MSCK REPAIR TABLE` command to update the metadata in the catalog after you add Hive compatible partitions\. 

The `MSCK REPAIR TABLE` command scans a file system such as Amazon S3 for Hive compatible partitions that were added to the file system after the table was created\. `MSCK REPAIR TABLE` compares the partitions in the table metadata and the partitions in S3\. If new partitions are present in the S3 location that you specified when you created the table, it adds those partitions to the metadata and to the Athena table\.

When you add physical partitions, the metadata in the catalog becomes inconsistent with the layout of the data in the file system, and information about the new partitions needs to be added to the catalog\. To update the metadata, run `MSCK REPAIR TABLE` so that you can query the data in the new partitions from Athena\.

**Note**  
`MSCK REPAIR TABLE` only adds partitions to metadata; it does not remove them\. To remove partitions from metadata after the partitions have been manually deleted in Amazon S3, run the command `ALTER TABLE table-name DROP PARTITION`\. For more information see [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md)\. 

## Considerations and Limitations<a name="msck-repair-table-considerations"></a>

When using `MSCK REPAIR TABLE`, keep in mind the following points:
+ Because the command traverses your file system running Amazon S3 [HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html) and [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) commands, the cost of bytes scanned can be significant if your file system is large or contains a large amount of data\.
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

After you run `MSCK REPAIR TABLE`, if Athena does not add the partitions to the table in the AWS Glue Data Catalog, check the following:
+ Make sure that the AWS Identity and Access Management \(IAM\) user or role has a policy that allows the `glue:BatchCreatePartition` action\.
+ Make sure that the IAM user or role has a policy with sufficient permissions to access Amazon S3, including the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DescribeJob.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DescribeJob.html) action\. For an example of which Amazon S3 actions to allow, see the example bucket policy in [Cross\-account Access in Athena to Amazon S3 Buckets](cross-account-permissions.md)\.
+ Make sure that the Amazon S3 path is in lower case instead of camel case \(for example, `userid` instead of `userId`\)\.
+ **Query timeouts** – `MSCK REPAIR TABLE` is best used when creating a table for the first time or when there is uncertainty about parity between data and partition metadata\. If you use `MSCK REPAIR TABLE` to add new partitions frequently \(for example, on a daily basis\) and are experiencing query timeouts, consider using [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\.
+ **Partitions missing from filesystem** – If you delete a partition manually in Amazon S3 and then run `MSCK REPAIR TABLE`, you may receive the error message Partitions missing from filesystem\. This occurs because `MSCK REPAIR TABLE` doesn't remove stale partitions from table metadata\. To remove the deleted partitions from table metadata, run [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md) instead\. Note that [SHOW PARTITIONS](show-partitions.md) similarly lists only the partitions in metadata, not the partitions in the file system\.

The following sections provide some additional detail\.

### Allow glue:BatchCreatePartition in the IAM policy<a name="msck-repair-table-troubleshooting-allow-gluebatchcreatepartition-in-the-IAM-policy"></a>

Review the IAM policies attached to the user or role that you're using to run `MSCK REPAIR TABLE`\. When you [use the AWS Glue Data Catalog with Athena](glue-athena.md), the IAM policy must allow the `glue:BatchCreatePartition` action\. For an example of an IAM policy that allows the `glue:BatchCreatePartition` action, see [AmazonAthenaFullAccess Managed Policy](amazonathenafullaccess-managed-policy.md)\.

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