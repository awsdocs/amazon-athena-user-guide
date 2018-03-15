# Best Practices for Handling Schema Updates<a name="updates-best-practices"></a>

If you anticipate changes in table schemas, consider creating them in a data format that best suits your needs\. 

For example, to rename columns but not delete them, create tables in CSV\. To delete columns, do not use CSV, and use any of the other supported formats, preferably, a format that supports compression, such as Parquet or ORC\. 

Your goals are to reuse existing Athena queries against evolving schemas and avoid schema mismatch errors when querying tables with partitions\. To achieve these goals, choose a table's data format based on the following tips\. 

+ *To add columns*, use any format\. If you use CSV with headers or without, you can add columns only at the end of your tables\. For information, see [Adding Columns](types-of-updates.md#updates-adding-columns)\.

+ *To delete columns*, use any format except CSV\. For information, see [Removing Columns](types-of-updates.md#updates-removing-columns)\.

+ *To rename columns*, store your data in CSV formats \(with headers and without\)\. 

  To enable column renaming in Parquet and ORC, set these SerDe properties `orc.column.index.access` for ORC, and `parquet.column.index.access` for Parquet to `TRUE`\. In this configuration, you will not be able to add or delete columns\. For information, see [Renaming Columns](types-of-updates.md#updates-renaming-columns)\.

+ *To change a column’s data type*, use data in any format but test your query in Athena to make sure the data types are compatible and can change from one to another\. For information, see [Changing a Column's Data Type](types-of-updates.md#updates-changing-column-type)\.

+ If you tried a different format, but the schema mismatch error persists, drop the partition that is causing the error and recreate the table\. For more information, see [Updates in Tables with Partitions](updates-and-partitions.md)\. 

The following table summarizes these principles to help you choose the format and continue using Athena queries even as you schemas change over time\. 

In this table, observe that PARQUET and ORC are columnar formats that let you perform all operations on schemas and use Athena queries without schema mismatch errors\. They also ensure best query performance\. CSV allows you to do all operations except deleting columns\.


**Updates and Data Formats in Athena**  

| Type of Schema Update | CSV \(with and without headers\) and TSV | JSON | AVRO | PARQUET | ORC | 
| --- | --- | --- | --- | --- | --- | 
| Rename Columns | Y | N | N | Y\. To enable column renaming, set the SerDe property `parquet.column.index.access` to `TRUE`\. You cannot add or delete columns in this case\. | Y\.To enable column renaming, set the SerDe property `orc.column.index.access` to `TRUE`\. You cannot add or delete columns in this case\. | 
| Add Columns | Y | Y | Y | Y | Y | 
| Delete Columns | N | Y | Y | Y | Y | 
| Change a Column's Type | Y | Y | Y | Y | Y | 