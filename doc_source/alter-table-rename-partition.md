# ALTER TABLE RENAME PARTITION<a name="alter-table-rename-partition"></a>

Renames a partition column, `partition_spec`, for the table named `table_name`, to `new_partition_spec`\.

For information about partitioning, see [Partitioning Data](partitions.md)\.

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name PARTITION (partition_spec) RENAME TO PARTITION (new_partition_spec)
```

## Parameters<a name="parameters"></a>

**PARTITION \(partition\_spec\)**  
Each `partition_spec` specifies a column name/value combination in the form `partition_col_name = partition_col_value [,...]`\.

## Examples<a name="examples"></a>

```
ALTER TABLE orders 
PARTITION (dt = '2014-05-14', country = 'IN') RENAME TO PARTITION (dt = '2014-05-15', country = 'IN');
```