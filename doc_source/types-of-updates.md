# Types of Updates<a name="types-of-updates"></a>

The following four operations constitute updates to a table’s schema: 

+ [Adding Columns](#updates-adding-columns)

+ [Removing Columns](#updates-removing-columns)

+ [Renaming Columns](#updates-renaming-columns)

+ [Changing a Column's Data Type](#updates-changing-column-type)

Each operation can happen for various reasons\. Depending on how you expect your schemas to evolve, to continue using Athena queries, choose a compatible data format\. 

Let’s consider an application that reads orders information from an `orders` table that exists in two formats: CSV and Parquet\. 

The following example creates a table in Parquet\.

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
LOCATION 's3://schema_updates/orders_ parquet /';
```

The following example creates the same table in CSV\.

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

## Adding Columns<a name="updates-adding-columns"></a>

Adding columns is one of the most frequent schema changes\. For example, you may add a new column to enrich the table with new data\. Or, you may add a new column if the source for an existing column has changed, and keep the previous version of this column, to adjust applications that depend on them\.

You can add new columns and continue running queries in Athena in the following cases\.

+ At the end of the table, if you create tables in any of the formats that Athena supports, such as Parquet, ORC, Avro, JSON, CSV, and TSV\.

+ At the beginning or in the middle of the table, if you create tables in ORC, Parquet, Avro, or JSON formats\.

+ If a table is in CSV or TSV, you can add a column at the end of the table\. Adding a column at the beginning or in the middle will lead to a schema mismatch error when you run your query\.

In this example, drop an existing table in Parquet, and add a new Parquet table with a new ``comment`` column\. 

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

In this example, drop an existing table in CSV and add a new CSV table with a new ``comment`` column\.

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

All data formats support addition of new columns at the end of the table, but not all data formats support addition of columns at the beginning or middle of a table\. To add new columns in the middle of the table, use ORC, Parquet, Avro, or JSON formats\. Do not use CSV and TSV, as these data types depend on ordering\.

 The following example illustrates these statements\. It shows adding a column to a JSON table in the middle of the table\.

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

## Removing Columns<a name="updates-removing-columns"></a>

You may need to remove columns from tables if they no longer contain data, or to restrict access to the data in them\.

+ You can remove columns from tables in JSON, Avro, Parquet, and ORC\. 

+ You cannot remove columns from tables in CSV and TSV\.

To remove columns, set the `“orc.column.index.access”` and the `“parquet.column.index.access”` SerDe properties to `FALSE`, in ORC and Parquet SerDes\. Note that setting these properties prevents you from renaming existing columns\. 

In this example, remove a column ``totalprice`` from a table in Parquet and run a query\. Notice that the query succeeds, even though you changed the schema\.

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
LOCATION 's3://schema_updates/tpch10/orders_parquet/';
```

## Renaming Columns<a name="updates-renaming-columns"></a>

You may want to rename columns in your tables to correct spelling, make column names more descriptive, or to reuse an existing column to avoid column reordering\.

Most data formats do not support renaming columns with the exception of CSV\. Athena reads data in CVS in the order of the columns in the schema and returns them in the same order\. It does not use column names for mapping data to a column, which is why you cannot rename columns in CVS without breaking Athena queries\. 

In this example, rename the column ``o_totalprice`` to ``o_total_price`` in the Parquet table, and then run a query in Athena\. 

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
LOCATION 's3://TBD/schema_updates/tpch10/orders_parquet/';
```

In the Parquet table case, the following query runs, but the renamed column does not show data\. 

```
SELECT * 
FROM orders_parquet_column_renamed;
```

A query with a table in CSV looks similar\.

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

In the CSV table case, the following query runs and the data displays in all columns, including the one that was renamed\.

```
SELECT * 
FROM orders_csv_column_renamed;
```

**Note**  
If you create tables in Parquet and ORC, you can also rename columns\. However, this configuration does not allow you to add and delete columns\. Within `Athena`, to rename columns, tables in ORC require `“orc.column.index.access”` to be set to `TRUE` in the ORC SerDe, and tables in Parquet require `“parquet.column.index.access”` to be set to `TRUE` in the Parquet SerDe\. With these settings, however, you cannot remove columns or add them in the middle of the table\. 

## Changing a Column's Data Type<a name="updates-changing-column-type"></a>

You change column types because a column's data type can no longer hold the amount of information, for example, when an ID column exceeds the size of an `INT` data type and has to change to a `BIGINT` data type\. Only certain data types can be converted to other data types\. 

**Note**  
Amazon strongly suggests that you test and verify your queries before performing data type translations\. If Athena cannot convert the data type from the original data type to the target data type, the `CREATE TABLE` query may fail or return `NULL`\. This depends on the data format\. 


**Compatible Data Types**  

| Original Data Type | Available Target Data Types | 
| --- | --- | 
| STRING | BYTE, TINYINT, SMALLINT, INT, BIGINT | 
| BYTE | TINYINT, SMALLINT, INT, BIGINT | 
| TINYINT | SMALLINT, INT, BIGINT | 
| SMALLINT | INT, BIGINT | 
| INT | BIGINT | 
| FLOAT | DOUBLE | 

In the following example of the `orders_json` table, change the data type for the column ``o_shippriority`` to `BIGINT`\.

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
LOCATION 's3://TBD/AwsBigDataBlog/tpch10/orders_json';
```

The following query runs successfully, similar to the original `SELECT` query, before the data type change\.

```
Select * from orders_json 
LIMIT 10;
```