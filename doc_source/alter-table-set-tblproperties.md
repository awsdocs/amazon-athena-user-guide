# ALTER TABLE SET TBLPROPERTIES<a name="alter-table-set-tblproperties"></a>

Adds custom or predefined metadata properties to a table and sets their assigned values\. To see the properties in a table, use the [SHOW TBLPROPERTIES](show-tblproperties.md) command\.

Apache Hive [Managed tables](https://cwiki.apache.org/confluence/display/Hive/Managed+vs.+External+Tables) are not supported, so setting `'EXTERNAL'='FALSE'` has no effect\.

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name SET TBLPROPERTIES ('property_name' = 'property_value' [ , ... ])
```

## Parameters<a name="parameters"></a>

**SET TBLPROPERTIES \('property\_name' = 'property\_value' \[ , \.\.\. \]\)**  
Specifies the metadata properties to add as `property_name` and the value for each as `property value`\. If `property_name` already exists, its value is set to the newly specified `property_value`\.  
The following predefined table properties have special uses\.     
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/alter-table-set-tblproperties.html)

## Examples<a name="examples"></a>

The following example adds a comment note to table properties\.

```
ALTER TABLE orders 
SET TBLPROPERTIES ('notes'="Please don't drop this table.");
```

The following example modifies the table `existing_table` to use Parquet file format with ZSTD compression and ZSTD compression level 4\.

```
ALTER TABLE existing_table 
SET TBLPROPERTIES ('parquet.compression' = 'ZSTD', 'compression_level' = 4)
```