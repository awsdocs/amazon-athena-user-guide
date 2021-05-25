# ALTER TABLE ADD PARTITION<a name="alter-table-add-partition"></a>

Creates one or more partition columns for the table\. Each partition consists of one or more distinct column name/value combinations\. A separate data directory is created for each specified combination, which can improve query performance in some circumstances\. Partitioned columns don't exist within the table data itself, so if you use a column name that has the same name as a column in the table itself, you get an error\. For more information, see [Partitioning Data](partitions.md)\.

In Athena, a table and its partitions must use the same data formats but their schemas may differ\. For more information, see [Updates in Tables with Partitions](updates-and-partitions.md)\.

For information about the resource\-level permissions required in IAM policies \(including `glue:CreatePartition`\), see [AWS Glue API Permissions: Actions and Resources Reference](https://docs.aws.amazon.com/glue/latest/dg/api-permissions-reference.html) and [Fine\-Grained Access to Databases and Tables in the AWS Glue Data Catalog](fine-grained-access-to-glue-resources.md)\. For troubleshooting information about permissions when using Athena, see the [Permissions](troubleshooting-athena.md#troubleshooting-athena-permissions) section of the [Troubleshooting in Athena](troubleshooting-athena.md) topic\.

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name ADD [IF NOT EXISTS]
  PARTITION
  (partition_col1_name = partition_col1_value
  [,partition_col2_name = partition_col2_value]
  [,...])
  [LOCATION 'location1']
  [PARTITION
  (partition_colA_name = partition_colA_value
  [,partition_colB_name = partition_colB_value
  [,...])]
  [LOCATION 'location2']
  [,...]
```

## Parameters<a name="parameters"></a>

When you add a partition, you specify one or more column name/value pairs for the partition and the Amazon S3 path where the data files for that partition reside\.

**\[IF NOT EXISTS\]**  
Causes the error to be suppressed if a partition with the same definition already exists\.

**PARTITION \(partition\_col\_name = partition\_col\_value \[,\.\.\.\]\)**  
Creates a partition with the column name/value combinations that you specify\. Enclose `partition_col_value` in string characters only if the data type of the column is a string\.

**\[LOCATION 'location'\]**  
Specifies the directory in which to store the partitions defined by the preceding statement\.

## Examples<a name="examples"></a>

```
ALTER TABLE orders ADD
  PARTITION (dt = '2016-05-14', country = 'IN');
```

```
ALTER TABLE orders ADD
  PARTITION (dt = '2016-05-14', country = 'IN')
  PARTITION (dt = '2016-05-15', country = 'IN');
```

```
ALTER TABLE orders ADD
  PARTITION (dt = '2016-05-14', country = 'IN') LOCATION 's3://mystorage/path/to/INDIA_14_May_2016/'
  PARTITION (dt = '2016-05-15', country = 'IN') LOCATION 's3://mystorage/path/to/INDIA_15_May_2016/';
```