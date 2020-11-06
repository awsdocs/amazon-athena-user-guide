# ALTER TABLE REPLACE COLUMNS<a name="alter-table-replace-columns"></a>

Removes all existing columns from a table created with the [LazySimpleSerDe](lazy-simple-serde.md) and replaces them with the set of columns specified\. When the optional `PARTITION` syntax is used, updates partition metadata\. You can also use `ALTER TABLE REPLACE COLUMNS` to drop columns by specifying only the columns that you want to keep\.

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name 
  [PARTITION 
   (partition_col1_name = partition_col1_value
   [,partition_col2_name = partition_col2_value][,...])]
  REPLACE COLUMNS (col_name data_type [, col_name data_type, ...])
```

## Parameters<a name="parameters"></a>

**PARTITION \(partition\_col\_name = partition\_col\_value \[,\.\.\.\]\)**  
Specifies a partition with the column name/value combinations that you specify\. Enclose `partition_col_value` in quotation marks only if the data type of the column is a string\.

**REPLACE COLUMNS \(col\_name data\_type \[,col\_name data\_type,…\]\)**  
Replaces existing columns with the column names and datatypes specified\.

## Notes<a name="alter-table-replace-columns-notes"></a>
+ To see the change in table columns in the Athena Query Editor navigation pane after you run `ALTER TABLE REPLACE COLUMNS`, manually refresh the table list in the editor, and then expand the table again\.
+ `ALTER TABLE REPLACE COLUMNS` does not work for columns with the `date` datatype\. To workaround this issue, use the `timestamp` datatype in the table instead\.