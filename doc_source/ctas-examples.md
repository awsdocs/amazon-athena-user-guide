# Examples of CTAS Queries<a name="ctas-examples"></a>

Use the following examples to create CTAS queries\. For information about the CTAS syntax, see [CREATE TABLE AS](create-table-as.md)\.

In this section: 
+ [Example 1: Selecting All Columns with CTAS](#ctas-example1)
+ [Example 2: Selecting Specific Columns from One or More Tables with CTAS](#ctas-example2)
+ [Example 3: Creating an Empty Copy of an Existing Table with CTAS](#ctas-example3)
+ [Example 4: Specifying Data Storage and Compression Formats for CTAS Query Results](#ctas-example4)
+ [Example 5: Storing Results of a CTAS Query in Another Format](#ctas-example-query-storage-format)
+ [Example 6: CTAS Queries without Partitions](#ctas-example-not-partitioned)
+ [Example 7: CTAS Queries with Partitions](#ctas-example-partitioned)
+ [Example 8: A CTAS Query with Bucketing](#ctas-example-bucketed)

**Example Example 1: Selecting All Columns with CTAS**  <a name="ctas-example1"></a>
The following example creates a table by copying all columns from a table:  

```
CREATE TABLE new_table AS 
SELECT * 
FROM old_table;
```
In the following variation of the same example, your `SELECT` statement also includes a `WHERE` clause\. In this case, the query selects only those columns from the table that satisfy the `WHERE` clause:   

```
CREATE TABLE new_table AS 
SELECT * 
FROM old_table 
WHERE condition;
```

**Example Example 2: Selecting Specific Columns from One or More Tables with CTAS**  <a name="ctas-example2"></a>
The following example creates a new query that runs on a set of columns from another table:  

```
CREATE TABLE new_table AS 
SELECT column_1, column_2, ... column_n 
FROM old_table;
```
This variation of the same example creates a new table from specific columns from multiple tables:   

```
CREATE TABLE new_table AS
SELECT column_1, column_2, ... column_n 
FROM old_table_1, old_table_2, ... old_table_n;
```

**Example Example 3: Creating an Empty Copy of an Existing Table with CTAS**  <a name="ctas-example3"></a>
The following example uses `WITH NO DATA` to create a new table that is empty and has the same schema as the original table:  

```
CREATE TABLE new_table 
WITH NO DATA
AS SELECT * 
FROM old_table;
```

**Example Example 4: Specifying Data Storage and Compression Formats for CTAS Query Results**  <a name="ctas-example4"></a>
The following example creates a new CTAS query that saves data in Parquet\. This allows you to change the storage format from the one used by the original table\. You can specify `PARQUET`, `ORC`, `AVRO`, `JSON`, and `TEXTFILE` in a similar way\.   
This example also specifies compression as `SNAPPY`\. If omitted, GZIP is used\. GZIP and SNAPPY are the supported compression formats for CTAS query results stored in Parquet and ORC\.   

```
CREATE TABLE new_table
WITH (
      format = 'Parquet',
      parquet_compression = 'SNAPPY')
AS SELECT *
FROM old_table;
```
The following example is similar, but it stores the CTAS query results in ORC, and uses the `orc_compression` parameter, to specify the compression format\. If you omit the compression format, Athena uses GZIP by default\.  

```
CREATE TABLE new_table
WITH (format = 'ORC',
      orc_compression = 'SNAPPY')
AS SELECT *
FROM old_table ;
```

**Example Example 5: Storing Results of a CTAS Query in Another Format**  <a name="ctas-example-query-storage-format"></a>
The following CTAS query takes the results from another query, which could be stored in CSV or another text format, and stores them in ORC:   

```
CREATE TABLE my_orc_ctas_table
WITH (
      external_location = 's3://my_athena_results/my_orc_stas_table/',
      format = 'ORC')
AS SELECT * 
FROM old_table;
```

**Example Example 6: CTAS Queries without Partitions**  <a name="ctas-example-not-partitioned"></a>
The following examples create tables that are not partitioned\. The table data is stored in different formats\. Some of these examples specify the external location\.   
The following example creates a CTAS query that stores the results as a text file:  

```
CREATE TABLE ctas_csv_unpartitioned 
WITH (
     format = 'TEXTFILE', 
     external_location = 's3://my_athena_results/ctas_csv_unpartitioned/') 
AS SELECT key1, name1, address1, comment1
FROM table1;
```
In the following example, results are stored in Parquet, and the default results location is used:  

```
CREATE TABLE ctas_parquet_unpartitioned 
WITH (format = 'PARQUET') 
AS SELECT key1, name1, comment1
FROM table1;
```
In the following query, the table is stored in JSON, and specific columns are selected from the original table's results:  

```
CREATE TABLE ctas_json_unpartitioned 
WITH (
     format = 'JSON',  
     external_location = 's3://my_athena_results/ctas_json_unpartitioned/') 
AS SELECT key1, name1, address1, comment1
FROM table1;
```
In the following example, the format is ORC:  

```
CREATE TABLE ctas_orc_unpartitioned 
WITH (
     format = 'ORC') 
AS SELECT key1, name1, comment1 
FROM table1;
```
In the following example, the format is Avro:  

```
CREATE TABLE ctas_avro_unpartitioned 
WITH (
     format = 'AVRO', 
     external_location = 's3://my_athena_results/ctas_avro_unpartitioned/') 
AS SELECT key1, name1, comment1
FROM table1;
```

**Example Example 7: CTAS Queries with Partitions**  <a name="ctas-example-partitioned"></a>
The following examples show `CREATE TABLE AS SELECT` queries for partitioned tables in different storage formats, using `partitioned_by`, and other properties in the `WITH` clause\. For syntax, see [CTAS Table Properties](create-table-as.md#ctas-table-properties)\. For more information about choosing the columns for partitioning, see [Bucketing vs Partitioning](bucketing-vs-partitioning.md)\.  
List partition columns at the end of the list of columns in the `SELECT` statement\. You can partition by more than one column and have up to 100 partitions\.

```
CREATE TABLE ctas_csv_partitioned 
WITH (
     format = 'TEXTFILE',  
     external_location = 's3://my_athena_results/ctas_csv_partitioned/', 
     partitioned_by = ARRAY['key1']) 
AS SELECT name1, address1, comment1, key1
FROM tables1;
```

```
CREATE TABLE ctas_json_partitioned 
WITH (
     format = 'JSON', 
     external_location = 's3://my_athena_results/ctas_json_partitioned/', 
     partitioned_by = ARRAY['key1']) 
AS select name1, address1, comment1, key1 
FROM table1;
```

**Example Example 8: A CTAS Query with Bucketing**  <a name="ctas-example-bucketed"></a>
The following example shows a `CREATE TABLE AS SELECT` query that uses both partitioning and bucketing for storing query results in Amazon S3\. The table results are partitioned and bucketed by different columns\. You can create an unlimited number of buckets and bucket by one or more columns\. For syntax, see [CTAS Table Properties](create-table-as.md#ctas-table-properties)\.  
For information about choosing the columns for bucketing, see [Bucketing vs Partitioning](bucketing-vs-partitioning.md)\.  

```
CREATE TABLE ctas_avro_bucketed 
WITH (
      format = 'AVRO', 
      external_location = 's3://my_athena_results/ctas_avro_bucketed/', 
      partitioned_by = ARRAY['nationkey'], 
      bucketed_by = ARRAY['mktsegment'], 
      bucket_count = 3) 
AS SELECT key1, name1, address1, phone1, acctbal, mktsegment, comment1, nationkey 
FROM table1;
```