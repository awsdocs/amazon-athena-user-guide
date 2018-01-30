# ALTER TABLE SET LOCATION<a name="alter-table-set-location"></a>

Changes the location for the table named `table_name`, and optionally a partition with `partition_spec`\.

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name [ PARTITION (partition_spec) ] SET LOCATION 'new location'
```

## Parameters<a name="parameters"></a>

**PARTITION \(partition\_spec\)**  
Specifies the partition with parameters `partition_spec` whose location you want to change\. The `partition_spec` specifies a column name/value combination in the form `partition_col_name = partition_col_value`\.

**SET LOCATION 'new location'**  
Specifies the new location, which must be an Amazon S3 location\.

## Examples<a name="examples"></a>

```
ALTER TABLE customers PARTITION (zip='98040', state='WA') SET LOCATION 's3://mystorage/custdata';
```