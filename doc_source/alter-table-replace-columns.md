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

**REPLACE COLUMNS \(col\_name data\_type \[,col\_name data\_type,\.\.\.\]\)**  
Replaces existing columns with the column names and datatypes specified\.

## Notes<a name="alter-table-replace-columns-notes"></a>
+ To see the change in table columns in the Athena Query Editor navigation pane after you run `ALTER TABLE REPLACE COLUMNS`, you might have to manually refresh the table list in the editor, and then expand the table again\.
+ `ALTER TABLE REPLACE COLUMNS` does not work for columns with the `date` datatype\. To workaround this issue, use the `timestamp` datatype in the table instead\.
+ Note that even if you are replacing just a single column, the syntax must be `ALTER TABLE table-name REPLACE COLUMNS`, with *columns* in the plural\. You must specify not only the column that you want to replace, but the columns that you want to keep – if not, the columns that you do not specify will be dropped\. This syntax and behavior derives from Apache Hive DDL\. For reference, see [Add/Replace columns](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Add/ReplaceColumns) in the Apache documentation\. 

## Example<a name="alter-table-replace-columns-example"></a>

In the following example, the table `names_cities`, which was created using the [LazySimpleSerDe](lazy-simple-serde.md), has three columns named `col1`, `col2`, and `col3`\. All columns are of type `string`\. To show the columns in the table, the following command uses the [SHOW COLUMNS](show-columns.md) statement\.

```
SHOW COLUMNS IN names_cities
```

Result of the query:

```
col1
col2
col3
```

The following `ALTER TABLE REPLACE COLUMNS` command replaces the column names with `first_name`, `last_name`, and `city`\. The underlying source data is not affected\.

```
ALTER TABLE names_cities
REPLACE COLUMNS (first_name string, last_name string, city string)
```

To test the result, `SHOW COLUMNS` is run again\.

```
SHOW COLUMNS IN names_cities
```

Result of the query:

```
first_name
last_name
city
```

Another way to show the new column names is to [preview the table](creating-tables.md#creating-tables-showing-table-information) in the Athena Query Editor or run your own `SELECT` query\.