# Types of Updates<a name="types-of-updates"></a>

Here are the types of updates that a table’s schema can have\. We review each type of schema update and specify which data formats allow you to have them in Athena\.

**Important**  
Schema updates described in this section do not work on tables with complex or nested data types, such as arrays and structs\.
+ [Adding Columns at the Beginning or Middle of the Table](#updates-add-columns-beginning-middle-of-table)
+ [Adding Columns at the End of the Table](#updates-add-columns-end-of-table)
+ [Removing Columns](#updates-removing-columns)
+ [Renaming Columns](#updates-renaming-columns)
+ [Reordering Columns](#updates-reordering-columns)
+ [Changing a Column's Data Type](#updates-changing-column-type)

Depending on how you expect your schemas to evolve, to continue using Athena queries, choose a compatible data format\. 

Let’s consider an application that reads orders information from an `orders` table that exists in two formats: CSV and Parquet\. 

The following example creates a table in Parquet:

```
CREATE EXTERNAL TABLE orders_parquet (
   `orderkey` int, 
   `orderstatus` string, 
   `totalprice` double, 
   `orderdate` string, 
   `orderpriority` string, 
   `clerk` string, 
   `shippriority` int
) STORED AS PARQUET
LOCATION 's3://schema_updates/orders_ parquet/';
```

The following example creates the same table in CSV:

```
CREATE EXTERNAL TABLE orders_csv (
   `orderkey` int, 
   `orderstatus` string, 
   `totalprice` double, 
   `orderdate` string, 
   `orderpriority` string, 
   `clerk` string, 
   `shippriority` int
) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION 's3://schema_updates/orders_csv/';
```

In the following sections, we review how updates to these tables affect Athena queries\.

## Adding Columns at the Beginning or in the Middle of the Table<a name="updates-add-columns-beginning-middle-of-table"></a>

Adding columns is one of the most frequent schema changes\. For example, you may add a new column to enrich the table with new data\. Or, you may add a new column if the source for an existing column has changed, and keep the previous version of this column, to adjust applications that depend on them\.

To add columns at the beginning or in the middle of the table, and continue running queries against existing tables, use AVRO, JSON, and Parquet and ORC if their SerDe property is set to read by name\. For information, see [Index Access in ORC and Parquet](handling-schema-updates-chapter.md#index-access)\.

Do not add columns at the beginning or in the middle of the table in CSV and TSV, as these formats depend on ordering\. Adding a column in such cases will lead to schema mismatch errors when the schema of partitions changes\.

 The following example shows adding a column to a JSON table in the middle of the table:

```
CREATE EXTERNAL TABLE orders_json_column_addition (
   `o_orderkey` int, 
   `o_custkey` int, 
   `o_orderstatus` string, 
   `o_comment` string, 
   `o_totalprice` double, 
   `o_orderdate` string, 
   `o_orderpriority` string, 
   `o_clerk` string, 
   `o_shippriority` int, 
   `o_comment` string
) 
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://schema_updates/orders_json/';
```

## Adding Columns at the End of the Table<a name="updates-add-columns-end-of-table"></a>

If you create tables in any of the formats that Athena supports, such as Parquet, ORC, Avro, JSON, CSV, and TSV, you can add new columns *at the end of the table*\. If you use ORC formats, you must configure ORC to read by name\. Parquet reads by name by default\. For information, see [Index Access in ORC and Parquet](handling-schema-updates-chapter.md#index-access)\.

In the following example, drop an existing table in Parquet, and add a new Parquet table with a new ``comment`` column at the end of the table: 

```
DROP TABLE orders_parquet;
CREATE EXTERNAL TABLE orders_parquet (
   `orderkey` int, 
   `orderstatus` string, 
   `totalprice` double, 
   `orderdate` string, 
   `orderpriority` string, 
   `clerk` string, 
   `shippriority` int
   `comment` string
) 
STORED AS PARQUET
LOCATION 's3://schema_updates/orders_parquet/';
```

In the following example, drop an existing table in CSV and add a new CSV table with a new ``comment`` column at the end of the table:

```
DROP TABLE orders_csv;
CREATE EXTERNAL TABLE orders_csv (
   `orderkey` int, 
   `orderstatus` string, 
   `totalprice` double, 
   `orderdate` string, 
   `orderpriority` string, 
   `clerk` string, 
   `shippriority` int
   `comment` string
) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION 's3://schema_updates/orders_csv/';
```

## Removing Columns<a name="updates-removing-columns"></a>

You may need to remove columns from tables if they no longer contain data, or to restrict access to the data in them\.
+ You can remove columns from tables in JSON, Avro, and in Parquet and ORC if they are read by name\. For information, see [Index Access in ORC and Parquet](handling-schema-updates-chapter.md#index-access)\. 
+ We do not recommend removing columns from tables in CSV and TSV if you want to retain the tables you have already created in Athena\. Removing a column breaks the schema and requires that you recreate the table without the removed column\.

In this example, remove a column ``totalprice`` from a table in Parquet and run a query\. In Athena, Parquet is read by name by default, this is why we omit the SERDEPROPERTIES configuration that specifies reading by name\. Notice that the following query succeeds, even though you changed the schema:

```
CREATE EXTERNAL TABLE orders_parquet_column_removed (
   `o_orderkey` int, 
   `o_custkey` int, 
   `o_orderstatus` string, 
   `o_orderdate` string, 
   `o_orderpriority` string, 
   `o_clerk` string, 
   `o_shippriority` int, 
   `o_comment` string
) 
STORED AS PARQUET
LOCATION 's3://schema_updates/orders_parquet/';
```

## Renaming Columns<a name="updates-renaming-columns"></a>

You may want to rename columns in your tables to correct spelling, make column names more descriptive, or to reuse an existing column to avoid column reordering\.

You can rename columns if you store your data in CSV and TSV, or in Parquet and ORC that are configured to read by index\. For information, see [Index Access in ORC and Parquet](handling-schema-updates-chapter.md#index-access)\. 

Athena reads data in CSV and TSV in the order of the columns in the schema and returns them in the same order\. It does not use column names for mapping data to a column, which is why you can rename columns in CSV or TSV without breaking Athena queries\. 

In this example, rename the column ``o_totalprice`` to ``o_total_price`` in the Parquet table, and then run a query in Athena: 

```
CREATE EXTERNAL TABLE orders_parquet_column_renamed (
   `o_orderkey` int, 
   `o_custkey` int, 
   `o_orderstatus` string, 
   `o_total_price` double, 
   `o_orderdate` string, 
   `o_orderpriority` string, 
   `o_clerk` string, 
   `o_shippriority` int, 
   `o_comment` string
) 
STORED AS PARQUET
LOCATION 's3://TBD/schema_updates/orders_parquet/';
```

In the Parquet table case, the following query runs, but the renamed column does not show data because the column was being accessed by name \(a default in Parquet\) rather than by index:

```
SELECT * 
FROM orders_parquet_column_renamed;
```

A query with a table in CSV looks similar:

```
CREATE EXTERNAL TABLE orders_csv_column_renamed (
   `o_orderkey` int, 
   `o_custkey` int, 
   `o_orderstatus` string, 
   `o_total_price` double, 
   `o_orderdate` string, 
   `o_orderpriority` string, 
   `o_clerk` string, 
   `o_shippriority` int, 
   `o_comment` string
) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION 's3://schema_updates/orders_csv/';
```

In the CSV table case, the following query runs and the data displays in all columns, including the one that was renamed:

```
SELECT * 
FROM orders_csv_column_renamed;
```

## Reordering Columns<a name="updates-reordering-columns"></a>

You can reorder columns only for tables with data in formats that read by name, such as JSON or ORC, which reads by name by default\. You can also make Parquet read by name, if needed\. For information, see [Index Access in ORC and Parquet](handling-schema-updates-chapter.md#index-access)\.

The following example illustrates reordering of columns:

```
CREATE EXTERNAL TABLE orders_parquet_columns_reordered (
   `o_comment` string,
   `o_orderkey` int, 
   `o_custkey` int, 
   `o_orderpriority` string, 
   `o_orderstatus` string, 
   `o_clerk` string, 
   `o_shippriority` int, 
   `o_orderdate` string
) 
STORED AS PARQUET
LOCATION 's3://schema_updates/orders_parquet/';
```

## Changing a Column's Data Type<a name="updates-changing-column-type"></a>

You change column types because a column's data type can no longer hold the amount of information, for example, when an ID column exceeds the size of an `INT` data type and has to change to a `BIGINT` data type\. Only certain data types can be converted to other data types\.

**Note**  
We strongly suggest that you test and verify your queries before performing data type translations\. If Athena cannot convert the data type from the original data type to the target data type, the `CREATE TABLE` query may fail\.

The following table lists data types that you can change:


**Compatible Data Types**  

| Original Data Type | Available Target Data Types | 
| --- | --- | 
| STRING | BYTE, TINYINT, SMALLINT, INT, BIGINT | 
| BYTE | TINYINT, SMALLINT, INT, BIGINT | 
| TINYINT | SMALLINT, INT, BIGINT | 
| SMALLINT | INT, BIGINT | 
| INT | BIGINT | 
| FLOAT | DOUBLE | 

In the following example of the `orders_json` table, change the data type for the column ``o_shippriority`` to `BIGINT`:

```
CREATE EXTERNAL TABLE orders_json (
   `o_orderkey` int, 
   `o_custkey` int, 
   `o_orderstatus` string, 
   `o_totalprice` double, 
   `o_orderdate` string, 
   `o_orderpriority` string, 
   `o_clerk` string, 
   `o_shippriority` BIGINT
) 
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://schema_updates/orders_json';
```

The following query runs successfully, similar to the original `SELECT` query, before the data type change:

```
Select * from orders_json 
LIMIT 10;
```