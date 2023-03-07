# MSCK REPAIR TABLE<a name="msck-repair-table"></a>

Use the `MSCK REPAIR TABLE` command to update the metadata in the catalog after you add Hive compatible partitions\. 

The `MSCK REPAIR TABLE` command scans a file system such as Amazon S3 for Hive compatible partitions that were added to the file system after the table was created\. `MSCK REPAIR TABLE` compares the partitions in the table metadata and the partitions in S3\. If new partitions are present in the S3 location that you specified when you created the table, it adds those partitions to the metadata and to the Athena table\.

When you add physical partitions, the metadata in the catalog becomes inconsistent with the layout of the data in the file system, and information about the new partitions needs to be added to the catalog\. To update the metadata, run `MSCK REPAIR TABLE` so that you can query the data in the new partitions from Athena\.

**Note**  
`MSCK REPAIR TABLE` only adds partitions to metadata; it does not remove them\. To remove partitions from metadata after the partitions have been manually deleted in Amazon S3, run the command `ALTER TABLE table-name DROP PARTITION`\. For more information see [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md)\. 

## Considerations and limitations<a name="msck-repair-table-considerations"></a>

When using `MSCK REPAIR TABLE`, keep in mind the following points:
+ It is possible it will take some time to add all partitions\. If this operation times out, it will be in an incomplete state where only a few partitions are added to the catalog\. You should run `MSCK REPAIR TABLE` on the same table until all partitions are added\. For more information, see [Partitioning data in Athena](partitions.md)\. 
+ For partitions that are not compatible with Hive, use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) to load the partitions so that you can query their data\.
+ Partition locations to be used with Athena must use the `s3` protocol \(for example, `s3://bucket/folder/`\)\. In Athena, locations that use other protocols \(for example, `s3a://bucket/folder/`\) will result in query failures when `MSCK REPAIR TABLE` queries are run on the containing tables\. 
+ Because `MSCK REPAIR TABLE` scans both a folder and its subfolders to find a matching partition scheme, be sure to keep data for separate tables in separate folder hierarchies\. For example, suppose you have data for table A in `s3://table-a-data` and data for table B in `s3://table-a-data/table-b-data`\. If both tables are partitioned by string, `MSCK REPAIR TABLE` will add the partitions for table B to table A\. To avoid this, use separate folder structures like `s3://table-a-data` and `s3://table-b-data` instead\. Note that this behavior is consistent with Amazon EMR and Apache Hive\.
+ Due to a known issue, `MSCK REPAIR TABLE` fails silently when partition values contain a colon \(`:`\) character \(for example, when the partition value is a timestamp\)\. As a workaround, use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\. 

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
+ Make sure that the AWS Identity and Access Management \(IAM\) role has a policy that allows the `glue:BatchCreatePartition` action\.
+ Make sure that the role has a policy with sufficient permissions to access Amazon S3, including the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DescribeJob.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DescribeJob.html) action\. For an example of which Amazon S3 actions to allow, see the example bucket policy in [Cross\-account access in Athena to Amazon S3 buckets](cross-account-permissions.md)\.
+ Make sure that the Amazon S3 path is in lower case instead of camel case \(for example, `userid` instead of `userId`\)\.
+ **Query timeouts** – `MSCK REPAIR TABLE` is best used when creating a table for the first time or when there is uncertainty about parity between data and partition metadata\. If you use `MSCK REPAIR TABLE` to add new partitions frequently \(for example, on a daily basis\) and are experiencing query timeouts, consider using [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\.
+ **Partitions missing from filesystem** – If you delete a partition manually in Amazon S3 and then run `MSCK REPAIR TABLE`, you may receive the error message Partitions missing from filesystem\. This occurs because `MSCK REPAIR TABLE` doesn't remove stale partitions from table metadata\. To remove the deleted partitions from table metadata, run [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md) instead\. Note that [SHOW PARTITIONS](show-partitions.md) similarly lists only the partitions in metadata, not the partitions in the file system\.
+ **"NullPointerException name is null" error**

  If you use the AWS Glue [CreateTable](https://docs.aws.amazon.com/glue/latest/webapi/API_CreateTable.html) API operation or the AWS CloudFormation [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-table.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-table.html) template to create a table for use in Athena without specifying the `TableType` property and then run a DDL query like `SHOW CREATE TABLE` or `MSCK REPAIR TABLE`, you can receive the error message FAILED: NullPointerException Name is null\. 

  To resolve the error, specify a value for the [TableInput](https://docs.aws.amazon.com/glue/latest/webapi/API_TableInput.html) `TableType` attribute as part of the AWS Glue `CreateTable` API call or [AWS CloudFormation template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-glue-table-tableinput.html)\. Possible values for `TableType` include `EXTERNAL_TABLE` or `VIRTUAL_VIEW`\.

  This requirement applies only when you create a table using the AWS Glue `CreateTable` API operation or the `AWS::Glue::Table` template\. If you create a table for Athena by using a DDL statement or an AWS Glue crawler, the `TableType` property is defined for you automatically\. 

The following sections provide some additional detail\.

### Allow glue:BatchCreatePartition in the IAM policy<a name="msck-repair-table-troubleshooting-allow-gluebatchcreatepartition-in-the-policy"></a>

Review the IAM policies attached to the role that you're using to run `MSCK REPAIR TABLE`\. When you [use the AWS Glue Data Catalog with Athena](glue-athena.md), the IAM policy must allow the `glue:BatchCreatePartition` action\. For an example of an IAM policy that allows the `glue:BatchCreatePartition` action, see [AWS managed policy: AmazonAthenaFullAccess](managed-policies.md#amazonathenafullaccess-managed-policy)\.

### Change the Amazon S3 path to lower case<a name="msck-repair-table-troubleshooting-change-the-amazon-s3-path-to-flat-case"></a>

The Amazon S3 path must be in lower case\. If the S3 path is in camel case, `MSCK REPAIR TABLE` doesn't add the partitions to the AWS Glue Data Catalog\. For example, if your S3 path is `userId`, the following partitions aren't added to the AWS Glue Data Catalog:

```
s3://bucket/path/userId=1/

s3://bucket/path/userId=2/

s3://bucket/path/userId=3/
```

To resolve this issue, one of the following can be done: -

1. Use flat case instead of camel case:

    ```
    s3://bucket/path/userid=1/

    s3://bucket/path/userid=2/

    s3://bucket/path/userid=3/
    ```
    
2. Use [ALTER TABLE ADD PARTITION](https://docs.aws.amazon.com/athena/latest/ug/alter-table-add-partition.html) and specify the location:
    
    ```
    ALTER TABLE table_name ADD [IF NOT EXISTS]
    PARTITION (userId=1)
    LOCATION 's3://bucket/path/userId=1/'
    PARTITION (userId=2)
    LOCATION 's3://bucket/path/userId=2/'
    PARTITION (userId=3)
    LOCATION 's3://bucket/path/userId=3/'
    ```
