# Updates in Tables with Partitions<a name="updates-and-partitions"></a>

In Athena, a table and its partitions must use the same data formats but their schemas may differ\. When you create a new partition, that partition usually inherits the schema of the table\. Over time, the schemas may start to differ\. Reasons include:
+ If your table's schema changes, the schemas for partitions are not updated to remain in sync with the table's schema\. 
+ The AWS Glue Crawler allows you to discover data in partitions with different schemas\. This means that if you create a table in Athena with AWS Glue, after the crawler finishes processing, the schemas for the table and its partitions may be different\.
+ If you add partitions directly using an AWS API\.

Athena processes tables with partitions successfully if they meet the following constraints\. If these constraints are not met, Athena issues a HIVE\_PARTITION\_SCHEMA\_MISMATCH error\. 
+ Each partition’s schema is compatible with the table's schema\. 
+ The table's data format allows the type of update you want to perform: add, delete, reorder columns, or change a column's data type\. 

  For example, for CSV and TSV formats, you can rename columns, add new columns at the end of the table, and change a column's data type if the types are compatible, but you cannot remove columns\. For other formats, you can add or remove columns, or change a column's data type to another if the types are compatible\. For information, see [Summary: Updates and Data Formats in Athena](handling-schema-updates-chapter.md#summary-of-updates)\. 

## Avoiding Schema Mismatch Errors for Tables with Partitions<a name="partitions-dealing-with-schema-mismatch-errors"></a>

At the beginning of query execution, Athena verifies the table's schema by checking that each column data type is compatible between the table and the partition\. 
+ For Parquet and ORC data storage types, Athena relies on the column names and uses them for its column name\-based schema verification\. This eliminates `HIVE_PARTITION_SCHEMA_MISMATCH` errors for tables with partitions in Parquet and ORC\. \(This is true for ORC if the SerDe property is set to access the index by name: `orc.column.index.access=FALSE`\. Parquet reads the index by name by default\)\.
+ For CSV, JSON, and Avro, Athena uses an index\-based schema verification\. This means that if you encounter a schema mismatch error, you should drop the partition that is causing a schema mismatch and recreate it, so that Athena can query it without failing\.

 Athena compares the table's schema to the partition schemas\. If you create a table in CSV, JSON, and AVRO in Athena with AWS Glue Crawler, after the Crawler finishes processing, the schemas for the table and its partitions may be different\. If there is a mismatch between the table's schema and the partition schemas, your queries fail in Athena due to the schema verification error similar to this: 'crawler\_test\.click\_avro' is declared as type 'string', but partition 'partition\_0=2017\-01\-17' declared column 'col68' as type 'double'\."

A typical workaround for such errors is to drop the partition that is causing the error and recreate it\. For more information, see [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md) and [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\.