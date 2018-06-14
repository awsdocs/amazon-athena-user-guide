# Handling Schema Updates<a name="handling-schema-updates-chapter"></a>

This section provides guidance on handling schema updates for various data formats\. Athena is a schema\-on\-read query engine\. This means that when you create a table in Athena, it applies schemas when reading the data\. It does not change or rewrite the underlying data\. 

If you anticipate changes in table schemas, consider creating them in a data format that is suitable for your needs\. Your goals are to reuse existing Athena queries against evolving schemas, and avoid schema mismatch errors when querying tables with partitions\.

**Important**  
Schema updates described in this section do not work on complex or nested data types such as arrays and structs\.

To achieve these goals, choose a table's data format based on the following tips: 
+ If you expect to add columns to your schema, use any format\. If your data is stored in the CSV format you can add columns only at the end of your tables\. For information, see [Adding Columns](types-of-updates.md#updates-adding-columns)\.
+ If you expect to delete columns, store your data in any format except CSV\. For information, see [Removing Columns](types-of-updates.md#updates-removing-columns)\.
+ If you expect to rename columns, store your data in CSV formats\. To enable column renaming in Parquet and ORC, set these SerDe properties `orc.column.index.access` for ORC, and `parquet.column.index.access` for Parquet to `TRUE`\. These properties are set via the `CREATE TABLE` statement\. In this configuration, you will not be able to add or delete columns\. For information, see [Renaming Columns](types-of-updates.md#updates-renaming-columns)\.
+ If you expect a reordering of columns, store your data in ORC formats\. For information, see [Reordering Columns](types-of-updates.md#updates-reordering-columns)\.
+ To change a column’s data type, store your data in any format but test your query in Athena to make sure the data types are compatible\. For information, see [Changing a Column's Data Type](types-of-updates.md#updates-changing-column-type)\.
+ If you tried a different format, but the schema mismatch error persists, drop \(delete\) the partition that is causing the error and recreate it\. For more information, see [Updates in Tables with Partitions](updates-and-partitions.md)\. 

To review all formats mapped to update operations in a single table, see [Summary: Updates and Data Formats in Athena](types-of-updates.md#summary-of-updates)\.

**Topics**
+ [Types of Updates](types-of-updates.md)
+ [Updates in Tables with Partitions](updates-and-partitions.md)