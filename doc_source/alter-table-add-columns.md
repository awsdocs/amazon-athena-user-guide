# ALTER TABLE ADD COLUMNS<a name="alter-table-add-columns"></a>

Adds one or more columns to an existing table\. When the optional `PARTITION` syntax is used, updates partition metadata\. 

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name 
  [PARTITION 
   (partition_col1_name = partition_col1_value
   [,partition_col2_name = partition_col2_value][,...])]
  ADD COLUMNS (col_name data_type)
```

## Parameters<a name="parameters"></a>

**PARTITION \(partition\_col\_name = partition\_col\_value \[,\.\.\.\]\)**  
Creates a partition with the column name/value combinations that you specify\. Enclose `partition_col_value` in quotation marks only if the data type of the column is a string\.

**ADD COLUMNS \(col\_name data\_type \[,col\_name data\_type,…\]\)**  
Adds columns after existing columns but before partition columns\.

## Examples<a name="examples"></a>

```
ALTER TABLE events ADD COLUMNS (eventowner string)
```

```
ALTER TABLE events PARTITION (awsregion='us-west-2') ADD COLUMNS (event string)
```

```
ALTER TABLE events PARTITION (awsregion='us- west-2') ADD COLUMNS (eventdescription string)
```

**Note**  
To see a new table column in the Athena Query Editor after you run `ALTER TABLE ADD COLUMNS`, manually refresh the table list in the editor, and then expand the table again\.