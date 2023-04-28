# Types of updates<a name="types-of-updates"></a>

This topic describes some of the changes that you can make to the schema in `CREATE TABLE` statements without actually altering your data\. We review each type of schema update and specify which data formats allow them in Athena\. To update a schema, you can in some cases use an `ALTER TABLE` command, but in other cases you do not actually modify an existing table\. Instead, you create a table with a new name that modifies the schema that you used in your original `CREATE TABLE` statement\.
+ [Adding columns at the beginning or in the middle of the table](#updates-add-columns-beginning-middle-of-table)
+ [Adding columns at the end of the table](#updates-add-columns-end-of-table)
+ [Removing columns](#updates-removing-columns)
+ [Renaming columns](#updates-renaming-columns)
+ [Reordering columns](#updates-reordering-columns)
+ [Changing a column's data type](#updates-changing-column-type)

Depending on how you expect your schemas to evolve, to continue using Athena queries, choose a compatible data format\. 

Consider an application that reads orders information from an `orders` table that exists in two formats: CSV and Parquet\. 

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
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_ parquet/';
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
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_csv/';
```

In the following sections, we review how updates to these tables affect Athena queries\.

## Adding columns at the beginning or in the middle of the table<a name="updates-add-columns-beginning-middle-of-table"></a>

Adding columns is one of the most frequent schema changes\. For example, you may add a new column to enrich the table with new data\. Or, you may add a new column if the source for an existing column has changed, and keep the previous version of this column, to adjust applications that depend on them\.

To add columns at the beginning or in the middle of the table, and continue running queries against existing tables, use AVRO, JSON, and Parquet and ORC if their SerDe property is set to read by name\. For information, see [Index access in ORC and parquet](handling-schema-updates-chapter.md#index-access)\.

Do not add columns at the beginning or in the middle of the table in CSV and TSV, as these formats depend on ordering\. Adding a column in such cases will lead to schema mismatch errors when the schema of partitions changes\.

 The following example creates a new table that adds an `o_comment` column in the middle of a table based on JSON data\.

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
) 
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_json/';
```

## Adding columns at the end of the table<a name="updates-add-columns-end-of-table"></a>

If you create tables in any of the formats that Athena supports, such as Parquet, ORC, Avro, JSON, CSV, and TSV, you can use the `ALTER TABLE ADD COLUMNS` statement to add columns after existing columns but before partition columns\.

The following example adds a `comment` column at the end of the `orders_parquet` table before any partition columns: 

```
ALTER TABLE orders_parquet ADD COLUMNS (comment string)
```

**Note**  
To see a new table column in the Athena Query Editor after you run `ALTER TABLE ADD COLUMNS`, manually refresh the table list in the editor, and then expand the table again\.

## Removing columns<a name="updates-removing-columns"></a>

You may need to remove columns from tables if they no longer contain data, or to restrict access to the data in them\.
+ You can remove columns from tables in JSON, Avro, and in Parquet and ORC if they are read by name\. For information, see [Index access in ORC and parquet](handling-schema-updates-chapter.md#index-access)\. 
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
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_parquet/';
```

## Renaming columns<a name="updates-renaming-columns"></a>

You may want to rename columns in your tables to correct spelling, make column names more descriptive, or to reuse an existing column to avoid column reordering\.

You can rename columns if you store your data in CSV and TSV, or in Parquet and ORC that are configured to read by index\. For information, see [Index access in ORC and parquet](handling-schema-updates-chapter.md#index-access)\. 

Athena reads data in CSV and TSV in the order of the columns in the schema and returns them in the same order\. It does not use column names for mapping data to a column, which is why you can rename columns in CSV or TSV without breaking Athena queries\. 

One strategy for renaming columns is to create a new table based on the same underlying data, but using new column names\. The following example creates a new `orders_parquet` table called `orders_parquet_column_renamed`\. The example changes the column ``o_totalprice`` name to ``o_total_price`` and then runs a query in Athena: 

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
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_parquet/';
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
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_csv/';
```

In the CSV table case, the following query runs and the data displays in all columns, including the one that was renamed:

```
SELECT * 
FROM orders_csv_column_renamed;
```

## Reordering columns<a name="updates-reordering-columns"></a>

You can reorder columns only for tables with data in formats that read by name, such as JSON or Parquet, which reads by name by default\. You can also make ORC read by name, if needed\. For information, see [Index access in ORC and parquet](handling-schema-updates-chapter.md#index-access)\.

The following example creates a new table with the columns in a different order:

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
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_parquet/';
```

## Changing a column's data type<a name="updates-changing-column-type"></a>

You might want to use a different column type when the existing type can no longer hold the amount of information required\. For example, an ID column's values might exceed the size of the `INT` data type and require the use of the `BIGINT` data type\. 

Using a new column data type has these limitations:
+ Only certain data types can be converted to other data types\. See the table in this section for data types that can change\.
+ For data in Parquet and ORC, you cannot change a column's data type if the table is not partitioned\. 

  For partitioned tables in Parquet and ORC, a partition's column type can be different from another partition's column type, and Athena will `CAST` to the desired type, if possible\. For information, see [Avoiding schema mismatch errors for tables with partitions](updates-and-partitions.md#partitions-dealing-with-schema-mismatch-errors)\.

**Important**  
We strongly suggest that you test and verify your queries before performing data type translations\. If Athena cannot use the target data type, the `CREATE TABLE` query may fail\. 

The following table lists data types that you can change:


**Compatible data types**  

| Original data type | Available target data types | 
| --- | --- | 
| STRING | BYTE, TINYINT, SMALLINT, INT, BIGINT | 
| BYTE | TINYINT, SMALLINT, INT, BIGINT | 
| TINYINT | SMALLINT, INT, BIGINT | 
| SMALLINT | INT, BIGINT | 
| INT | BIGINT | 
| FLOAT | DOUBLE | 

The following example uses the `CREATE TABLE` statement for the original `orders_json` table to create a new table called `orders_json_bigint`\. The new table uses `BIGINT` instead of `INT` as the data type for the ``o_shippriority`` column\. 

```
CREATE EXTERNAL TABLE orders_json_bigint (
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
LOCATION 's3://DOC-EXAMPLE-BUCKET/orders_json';
```

The following query runs successfully, similar to the original `SELECT` query, before the data type change:

```
Select * from orders_json 
LIMIT 10;
```