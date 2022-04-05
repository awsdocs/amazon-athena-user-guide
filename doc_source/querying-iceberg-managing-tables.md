# Managing Iceberg Tables<a name="querying-iceberg-managing-tables"></a>

Athena supports the following table DDL operations for Iceberg tables\.

## ALTER TABLE RENAME<a name="querying-iceberg-alter-table-rename"></a>

Renames a table\.

Because the table metadata of an Iceberg table is stored in Amazon S3, you can update the database and table name of an Iceberg managed table without affecting underlying table information\.

### Synopsis<a name="querying-iceberg-alter-table-rename-synopsis"></a>

```
ALTER TABLE [db_name.]table_name RENAME TO [new_db_name.]new_table_name
```

### Example<a name="querying-iceberg-alter-table-rename-example"></a>

```
ALTER TABLE my_db.my_table RENAME TO my_db2.my_table2
```

## ALTER TABLE SET PROPERTIES<a name="querying-iceberg-alter-table-set-properties"></a>

Adds properties to an Iceberg table and sets their assigned values\.

In accordance with [Iceberg specifications](https://iceberg.apache.org/#spec/#table-metadata-fields), table properties are stored in the Iceberg table metadata file rather than in AWS Glue\. Athena does not accept custom table properties\. Refer to the [Table Properties](querying-iceberg-creating-tables.md#querying-iceberg-table-properties) section for allowed key\-value pairs\. If you would like Athena to support a specific open source table configuration property, send feedback to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\.

### Synopsis<a name="querying-iceberg-alter-table-set-properties-synopsis"></a>

```
ALTER TABLE [db_name.]table_name SET TBLPROPERTIES ('property_name' = 'property_value' [ , ... ])
```

### Example<a name="querying-iceberg-alter-table-set-properties-example"></a>

```
ALTER TABLE iceberg_table SET TBLPROPERTIES (
  'write_target_data_file_size_bytes'='536870912', 
  'optimize_rewrite_delete_file_threshold'='10'
)
```

## ALTER TABLE UNSET PROPERTIES<a name="querying-iceberg-alter-table-unset-properties"></a>

Drops existing properties from an Iceberg table\.

### Synopsis<a name="querying-iceberg-alter-table-unset-properties-synopsis"></a>

```
ALTER TABLE [db_name.]table_name UNSET TBLPROPERTIES ('property_name' [ , ... ])
```

### Example<a name="querying-iceberg-alter-table-unset-properties-example"></a>

```
ALTER TABLE iceberg_table UNSET TBLPROPERTIES ('write_target_data_file_size_bytes')
```

## DESCRIBE TABLE<a name="querying-iceberg-describe-table"></a>

Describes table information\.

### Synopsis<a name="querying-iceberg-describe-table-synopsis"></a>

```
DESCRIBE [FORMATTED] [db_name.]table_name
```

When the `FORMATTED` option is specified, the output displays additional information such as table location and properties\.

### Example<a name="querying-iceberg-describe-table-example"></a>

```
DESCRIBE iceberg_table
```

## DROP TABLE<a name="querying-iceberg-drop-table"></a>

Drops an Iceberg table\.

**Warning**  
Because Iceberg tables are considered managed tables in Athena, dropping an Iceberg table also removes all the data in the table\.

### Synopsis<a name="querying-iceberg-drop-table-synopsis"></a>

```
DROP TABLE [IF EXISTS] [db_name.]table_name
```

### Example<a name="querying-iceberg-drop-table-example"></a>

```
DROP TABLE iceberg_table
```

## SHOW CREATE TABLE<a name="querying-iceberg-show-create-table"></a>

Displays a `CREATE TABLE` DDL statement that can be used to recreate the Iceberg table in Athena\. If Athena cannot reproduce the table structure \(for example, because custom table properties are specified in the table\), an UNSUPPORTED error is thrown\.

### Synopsis<a name="querying-iceberg-show-create-table-synopsis"></a>

```
SHOW CREATE TABLE [db_name.]table_name
```

### Example<a name="querying-iceberg-show-create-table-example"></a>

```
SHOW CREATE TABLE iceberg_table
```

## SHOW TABLE PROPERTIES<a name="querying-iceberg-show-table-properties"></a>

Shows one or more table properties of an Iceberg table\. Only Athena\-supported table properties are shown\.

### Synopsis<a name="querying-iceberg-show-table-properties-synopsis"></a>

```
SHOW TBLPROPERTIES [db_name.]table_name [('property_name')]
```

### Example<a name="querying-iceberg-show-table-properties-example"></a>

```
SHOW TBLPROPERTIES iceberg_table
```