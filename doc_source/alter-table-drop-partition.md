# ALTER TABLE DROP PARTITION<a name="alter-table-drop-partition"></a>

Drops one or more specified partitions for the named table\.

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name DROP [IF EXISTS] PARTITION (partition_spec) [, PARTITION (partition_spec)]
```

## Parameters<a name="parameters"></a>

**\[IF EXISTS\]**  
Suppresses the error message if the partition specified does not exist\.

**PARTITION \(partition\_spec\)**  
Each `partition_spec` specifies a column name/value combination in the form `partition_col_name = partition_col_value [,...]`\.

## Examples<a name="examples"></a>

```
ALTER TABLE orders DROP PARTITION (dt = '2014-05-14', country = 'IN');
```

```
ALTER TABLE orders DROP PARTITION (dt = '2014-05-14', country = 'IN'), PARTITION (dt = '2014-05-15', country = 'IN');
```