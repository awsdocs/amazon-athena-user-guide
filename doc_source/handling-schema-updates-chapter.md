# Handling Schema Updates<a name="handling-schema-updates-chapter"></a>

This section provides guidance on handling schema updates for various data formats\. Athena is a schema\-on\-read query engine\. This means that when you create a table in Athena, it applies schemas when reading the data\. It does not change or rewrite the underlying data\. 

If you anticipate changes in table schemas, consider creating them in a data format that is suitable for your needs\. Your goals are to reuse existing Athena queries against evolving schemas, and avoid schema mismatch errors when querying tables with partitions\.

**Important**  
Schema updates described in this section do not work on tables with complex or nested data types, such as arrays and structs\.

To achieve these goals, choose a table's data format based on the table in the following topic\.

**Topics**
+ [Summary: Updates and Data Formats in Athena](#summary-of-updates)
+ [Index Access in ORC and Parquet](#index-access)
+ [Types of Updates](types-of-updates.md)
+ [Updates in Tables with Partitions](updates-and-partitions.md)

## Summary: Updates and Data Formats in Athena<a name="summary-of-updates"></a>

The following table summarizes data storage formats and their supported schema manipulations\. Use this table to help you choose the format that will enable you to continue using Athena queries even as your schemas change over time\. 

In this table, observe that Parquet and ORC are columnar formats with different default column access methods\. By default, Parquet will access columns by name and ORC by index \(ordinal value\)\. Therefore, Athena provides a SerDe property defined when creating a table to toggle the default column access method which enables greater flexibility with schema evolution\. 

For Parquet, the `parquet.column.index.access` property may be set to `TRUE`, which sets the column access method to use the column’s ordinal number\. Setting this property to `FALSE` will change the column access method to use column name\. Similarly, for ORC use the `orc.column.index.access` property to control the column access method\. For more information, see [Index Access in ORC and Parquet](#index-access)\.

CSV and TSV allow you to do all schema manipulations except reordering of columns, or adding columns at the beginning of the table\. For example, if your schema evolution requires only renaming columns but not removing them, you can choose to create your tables in CSV or TSV\. If you require removing columns, do not use CSV or TSV, and instead use any of the other supported formats, preferably, a columnar format, such as Parquet or ORC\.


**Schema Updates and Data Formats in Athena**  

| Expected Type of Schema Update | Summary | CSV \(with and without headers\) and TSV | JSON | AVRO | PARQUET: Read by Name \(default\) | PARQUET: Read by Index | ORC: Read by Index \(default\) | ORC: Read by Name | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
|  [Rename columns](types-of-updates.md#updates-renaming-columns) | Store your data in CSV and TSV, or in ORC and Parquet if they are read by index\. | Y | N | N | N  | Y | Y | N | 
|  [Add columns at the beginning or in the middle of the table](types-of-updates.md#updates-add-columns-beginning-middle-of-table) | Store your data in JSON, AVRO, or in Parquet and ORC if they are read by name\. Do not use CSV and TSV\. | N | Y | Y | Y | N | N | Y | 
|  [Add columns at the end of the table](types-of-updates.md#updates-add-columns-end-of-table) | Store your data in CSV or TSV, JSON, AVRO, and in ORC and Parquet if they are read by name\. | Y | Y | Y | Y | N | N | Y | 
| [Remove columns](types-of-updates.md#updates-removing-columns) |  Store your data in JSON, AVRO, or Parquet and ORC, if they are read by name\. Do not use CSV and TSV\. | N | Y | Y | Y | N | N | Y | 
| [Reorder columns](types-of-updates.md#updates-reordering-columns) | Store your data in AVRO, JSON or ORC and Parquet if they are read by name\. | N | Y | Y | Y | N | N | Y | 
| [Change a column's data type](types-of-updates.md#updates-changing-column-type) | Store your data in any format, but test your query in Athena to make sure the data types are compatible\. For Parquet and ORC, changing a data type works only for partitioned tables\. | Y | Y | Y | Y | Y | Y | Y | 

## Index Access in ORC and Parquet<a name="index-access"></a>

PARQUET and ORC are columnar data storage formats that can be read by index, or by name\. Storing your data in either of these formats lets you perform all operations on schemas and run Athena queries without schema mismatch errors\. 
+ Athena *reads ORC by index by default*, as defined in `SERDEPROPERTIES ( 'orc.column.index.access'='true')`\. For more information, see [ORC: Read by Index](#orc-read-by-index)\.
+ Athena reads *Parquet by name by default*, as defined in `SERDEPROPERTIES ( 'parquet.column.index.access'='false')`\. For more information, see [PARQUET: Read by Name](#parquet-read-by-name)\.

Since these are defaults, specifying these SerDe properties in your `CREATE TABLE` queries is optional, they are used implicitly\. When used, they allow you to run some schema update operations while preventing other such operations\. To enable those operations, run another `CREATE TABLE` query and change the SerDe settings\. 

**Note**  
The SerDe properties are *not* automatically propagated to each partition\. Use `ALTER TABLE ADD PARTITION` statements to set the SerDe properties for each partition\. To automate this process, write a script that runs `ALTER TABLE ADD PARTITION` statements\.

The following sections describe these cases in detail\.

### ORC: Read by Index<a name="orc-read-by-index"></a>

A table in *ORC is read by index*, by default\. This is defined by the following syntax:

```
WITH SERDEPROPERTIES ( 
  'orc.column.index.access'='true')
```

*Reading by index* allows you to rename columns\. But then you lose the ability to remove columns or add them in the middle of the table\. 

To make ORC read by name, which will allow you to add columns in the middle of the table or remove columns in ORC, set the SerDe property `orc.column.index.access` to `FALSE` in the `CREATE TABLE` statement\. In this configuration, you will lose the ability to rename columns\.

The following example illustrates how to change the ORC to make it read by name:

```
CREATE EXTERNAL TABLE orders_orc_read_by_name (
   `o_comment` string,
   `o_orderkey` int, 
   `o_custkey` int, 
   `o_orderpriority` string, 
   `o_orderstatus` string, 
   `o_clerk` string, 
   `o_shippriority` int, 
   `o_orderdate` string
) 
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.orc.OrcSerde' 
WITH SERDEPROPERTIES ( 
  'orc.column.index.access'='false') 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.orc.OrcInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.orc.OrcOutputFormat'
LOCATION 's3://schema_updates/orders_orc/';
```

### Parquet: Read by Name<a name="parquet-read-by-name"></a>

A table in *Parquet is read by name*, by default\. This is defined by the following syntax:

```
WITH SERDEPROPERTIES ( 
  'parquet.column.index.access'='false')
```

*Reading by name* allows you to add columns in the middle of the table and remove columns\. But then you lose the ability to rename columns\. 

To make Parquet read by index, which will allow you to rename columns, you must create a table with `parquet.column.index.access` SerDe property set to `TRUE`\.