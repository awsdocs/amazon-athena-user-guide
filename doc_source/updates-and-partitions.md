# Updates in Tables with Partitions<a name="updates-and-partitions"></a>

Tables that are partitioned store metadata, including schema, for each partition the table has\. In Athena, a table and its partitions must use the same data formats, such as, if a table is in Parquet, its partitions must also be in Parquet\. However, a table’s schema and its partitions schema *can* differ from each other\. 

When you create a new partition, that partition usually takes the schema of the table\. Over time, the schemas may start to differ for any of these reasons: 

+  If your table's schema changes, the schemas for partitions are not updated to remain in sync with the table's schema\. 

+ If you create a table in Athena with AWS Glue, its Crawler does not keep schemas consistent between a table and its partitions\. It may discover data in partitions with different schemas\. 

+ Lastly, if you add partitions directly using an AWS API\.

Athena processes tables with partitions successfully if they meet these constraints\. If these constraints are not met, Athena issues a HIVE\_PARTITION\_SCHEMA\_MISMATCH error\. 

+ Each partition’s schema is compatible with the table's schema\. 

+ The table's data format allows the type of update you want to perform: add, delete, or reorder columns, or change a column's data type\. 

  For example, for CSV format, you can only rename columns, add new columns at the end of the table, and change a column's data type if the types are compatible, but you cannot delete columns\. For other formats, you can add or delete columns, or change a column's data type to another if the types are compatible\. For information, see [Best Practices for Handling Schema Updates](updates-best-practices.md)\. 

## Avoiding Schema Mismatch Errors for Tables with Partitions<a name="partitions-dealing-with-schema-mismatch-errors"></a>

When AWS Glue Crawler runs on a partitioned table, it stores the schema of the partition together with the partition data\. Once the crawling process completes, the Crawler determines the table's schema based on the schema of partitions\. If there is a mismatch between the table's schema and the partition schemas, your queries in Athena fail due to the schema verification error similar to this: 'crawler\_liveintent\.click\_avro' is declared as type 'string', but partition 'partition\_0=2015\-01\-17' declared column 'col68' as type 'double'\."

 A typical workaround for such errors is to drop the partition that is causing the error and recreate the table\.

At the beginning of query execution, Athena verifies the table's schema by checking that the column data type is compatible between the table and the partition, for each column\. When Athena processes each partition, it determines which schema verification method to use: by column name or index\-based\. 

+ For Parquet, ORC, and JSON file types, Athena relies on the Parquet column names and uses them for its column name\-based schema verification\. This eliminates HIVE\_PARTITION\_SCHEMA\_MISMATCH errors for tables with partitions in Parquet, ORC, and JSON file types\. 

+ For CSV, Avro, arrays, structs, maps, and other advanced data types, Athena uses an index\-based schema verification\. This means that if you encounter a schema mismatch error, you should drop the partition that is causing a schema mismatch and recreate the table, so that Athena can query it without failing\. 