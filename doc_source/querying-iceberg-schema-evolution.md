# Schema Evolution<a name="querying-iceberg-schema-evolution"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

Iceberg schema updates are metadata\-only changes\. No data files are changed when you perform a schema update\. 

The Iceberg format supports the following schema evolution changes:
+ **Add** – Adds a new column to a table or to a nested `struct`\.
+ **Drop** – Removes an existing column from a table or nested `struct`\.
+ **Rename** – Renames an existing column or field in a nested `struct`\.
+ **Update** – Widens the type of a column, `struct` field, `map` key, `map` value, or `list` element\.
+ **Reorder** – Changes the order of columns or fields in a nested `struct`\.

Of these schema evolution changes, the Athena public preview currently supports only **Add**\.

**Note**  
If you would like Athena to support additional schema evolution changes, send feedback to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\.

## ALTER TABLE ADD COLUMNS<a name="querying-iceberg-alter-table-add-columns"></a>

Adds one or more columns to an existing Iceberg table\.

Because of the design of [hidden partitioning](https://iceberg.apache.org/#partitioning/#what-does-iceberg-do-differently) in Iceberg, and unlike [ALTER TABLE ADD COLUMNS](alter-table-add-columns.md) support for Athena Hive tables, Iceberg tables do not allow `PARTITION` syntax\.

### Synopsis<a name="querying-iceberg-alter-table-add-columns-synopsis"></a>

```
ALTER TABLE table_name ADD COLUMNS (col_name data_type [,...])
```

### Example<a name="querying-iceberg-alter-table-add-columns-example"></a>

```
ALTER TABLE iceberg_table ADD COLUMNS (comment string)
```