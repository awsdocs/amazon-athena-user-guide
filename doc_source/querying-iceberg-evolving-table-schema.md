# Evolving Iceberg Table Schema<a name="querying-iceberg-evolving-table-schema"></a>

Iceberg schema updates are metadata\-only changes\. No data files are changed when you perform a schema update\. 

The Iceberg format supports the following schema evolution changes:
+ **Add** – Adds a new column to a table or to a nested `struct`\.
+ **Drop** – Removes an existing column from a table or nested `struct`\.
+ **Rename** – Renames an existing column or field in a nested `struct`\.
+ **Reorder** – Changes the order of columns\.
+  **Type promotion** – Widens the type of a column, `struct` field, `map` key, `map` value, or `list` element\. Currently, the following cases are supported for Iceberg tables: 
  + integer to big integer
  + float to double
  + increasing the precision of a decimal type

## ALTER TABLE ADD COLUMNS<a name="querying-iceberg-alter-table-add-columns"></a>

Adds one or more columns to an existing Iceberg table\.

### Synopsis<a name="querying-iceberg-alter-table-add-columns-synopsis"></a>

```
ALTER TABLE [db_name.]table_name ADD COLUMNS (col_name data_type [,...])
```

### Example<a name="querying-iceberg-alter-table-add-columns-example"></a>

```
ALTER TABLE iceberg_table ADD COLUMNS (comment string)
```

## ALTER TABLE DROP COLUMN<a name="querying-iceberg-alter-table-drop-column"></a>

Drops a column from an existing Iceberg table\.

### Synopsis<a name="querying-iceberg-alter-table-drop-column-synopsis"></a>

```
ALTER TABLE [db_name.]table_name DROP COLUMN col_name
```

### Example<a name="querying-iceberg-alter-table-drop-column-example"></a>

```
ALTER TABLE iceberg_table DROP COLUMN userid
```

## ALTER TABLE CHANGE COLUMN<a name="querying-iceberg-alter-table-change-column"></a>

Changes the name, type, order or comment of a column\.

**Note**  
`ALTER TABLE REPLACE COLUMNS` is not supported\. Because `REPLACE COLUMNS` removes all columns and then adds new ones, it is not supported for Iceberg\. `CHANGE COLUMN` is the preferred syntax for schema evolution\. 

### Synopsis<a name="querying-iceberg-alter-table-change-column-synopsis"></a>

```
ALTER TABLE [db_name.]table_name
  CHANGE [COLUMN] col_old_name col_new_name column_type 
  [COMMENT col_comment] [FIRST|AFTER column_name]
```

### Example<a name="querying-iceberg-alter-table-change-column-example"></a>

```
ALTER TABLE iceberg_table CHANGE comment blog_comment string AFTER id
```

## SHOW COLUMNS<a name="querying-iceberg-show-columns"></a>

Shows the columns in a table\.

### Synopsis<a name="querying-iceberg-show-columns-synopsis"></a>

```
SHOW COLUMNS (FROM|IN) [db_name.]table_name
```

### Example<a name="querying-iceberg-alter-table-change-column-example"></a>

```
SHOW COLUMNS FROM iceberg_table
```