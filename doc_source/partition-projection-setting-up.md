# Setting up partition projection<a name="partition-projection-setting-up"></a>

Setting up partition projection in a table's properties is a two\-step process:

1. Specify the data ranges and relevant patterns for each partition column, or use a custom template\.

1. Enable partition projection for the table\.

**Note**  
Before you add partition projection properties to an existing table, the partition column for which you are setting up partition projection properties must already exist in the table schema\. If the partition column does not yet exist, you must add a partition column to the existing table manually\. AWS Glue does not perform this step for you automatically\. 

This section shows how to set the table properties for AWS Glue\. To set them, you can use the AWS Glue console, Athena [CREATE TABLE](create-table.md) queries, or [AWS Glue API](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api.html) operations\. The following procedure shows how to set the properties in the AWS Glue console\.

**To configure and enable partition projection using the AWS Glue console**

1. Sign in to the AWS Management Console and open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. Choose the **Tables** tab\.

   On the **Tables** tab, you can edit existing tables, or choose **Add tables** to create new ones\. For information about adding tables manually or with a crawler, see [Working with tables on the AWS Glue console](https://docs.aws.amazon.com/glue/latest/dg/console-tables.html) in the *AWS Glue Developer Guide*\.

1. In the list of tables, choose the link for the table that you want to edit\.  
![\[In the AWS Glue console, choose a table to edit.\]](http://docs.aws.amazon.com/athena/latest/ug/images/partition-projection-1.png)

1. Choose **Edit table**\.  
![\[Choose Edit table in the AWS Glue console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/partition-projection-2.png)

1. In the **Edit table details** dialog box, in the **Table properties** section, for each partitioned column, add the following key\-value pair:

   1. For **Key**, add `projection.columnName.type`\.

   1. For **Value**, add one of the supported types: `enum`, `integer`, `date`, or `injected`\. For more information, see [Supported types for partition projection](partition-projection-supported-types.md)\.

1. Following the guidance in [Supported types for partition projection](partition-projection-supported-types.md), add additional key\-value pairs according to your configuration requirements\.

   The following example table configuration configures the `year` column for partition projection, restricting the values that can be returned to a range from 2010 through 2016\.  
![\[Configuring partition projection for a partition column in the AWS Glue console table properties.\]](http://docs.aws.amazon.com/athena/latest/ug/images/partition-projection-3.png)

1. Add a key\-value pair to enable partition projection\. For **Key**, enter `projection.enabled`, and for its **Value**, enter `true`\.  
![\[Enabling partition projection in the properties for a table in the AWS Glue console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/partition-projection-4.png)
**Note**  
You can disable partition projection on this table at any time by setting `projection.enabled` to `false`\.

1. When you are finished, choose **Apply**\.

1. In the Athena Query Editor, test query the columns that you configured for the table\.

   The following example query uses `SELECT DISTINCT` to return the unique values from the `year` column\. The database contains data from 1987 to 2016, but the `projection.year.range` property restricts the values returned to the years 2010 to 2016\.  
![\[Querying a column that uses partition projection.\]](http://docs.aws.amazon.com/athena/latest/ug/images/partition-projection-5.png)
**Note**  
If you set `projection.enabled` to `true` but fail to configure one or more partition columns, you receive an error message like the following:  
`HIVE_METASTORE_ERROR: Table database_name.table_name is configured for partition projection, but the following partition columns are missing projection configuration: [column_name] (table database_name.table_name)`\.

## Specifying custom S3 storage locations<a name="partition-projection-specifying-custom-s3-storage-locations"></a>

When you edit table properties in AWS Glue, you can also specify a custom Amazon S3 path template for the projected partitions\. A custom template enables Athena to properly map partition values to custom Amazon S3 file locations that do not follow a typical `.../column=value/...` pattern\. 

Using a custom template is optional\. However, if you use a custom template, the template must contain a placeholder for each partition column\. Templated locations must end with a forward slash so that the partitioned data files live in a "folder" per partition\.

**To specify a custom partition location template**

1. Following the steps to [configure and enable partition projection using the AWS Glue console](#partition-projection-setting-up-procedure), add an additional a key\-value pair that specifies a custom template as follows:

   1. For **Key**, enter `storage.location.template`\.

   1. For **Value**, specify a location that includes a placeholder for every partition column\. Make sure that each placeholder \(and the S3 path itself\) is terminated by a single forward slash\.

      The following example template values assume a table with partition columns `a`, `b`, and `c`\.

      ```
      s3://bucket/table_root/a=${a}/${b}/some_static_subdirectory/${c}/      
      ```

      ```
      s3://bucket/table_root/c=${c}/${b}/some_static_subdirectory/${a}/${b}/${c}/${c}/      
      ```

      For the same table, the following example template value is invalid because it contains no placeholder for column `c`\.

      ```
      s3://bucket/table_root/a=${a}/${b}/some_static_subdirectory/         
      ```

1. Choose **Apply**\.