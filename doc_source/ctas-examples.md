# Examples of CTAS queries<a name="ctas-examples"></a>

Use the following examples to create CTAS queries\. For information about the CTAS syntax, see [CREATE TABLE AS](create-table-as.md)\.

In this section: 
+  [Example: Duplicating a table by selecting all columns](#ctas-example-dupe-table) 
+  [Example: Selecting specific columns from one or more tables](#ctas-example-specify-columns) 
+  [Example: Creating an empty copy of an existing table](#ctas-example-empty-table) 
+  [Example: Specifying data storage and compression formats](#ctas-example-compression) 
+  [Example: Writing query results to a different format](#ctas-example-format) 
+  [Example: Creating unpartitioned tables](#ctas-example-unpartitioned) 
+  [Example: Creating partitioned tables](#ctas-example-partitioned) 
+  [Example: Creating bucketed and partitioned tables](#ctas-example-bucketed) 
+  [Example: Creating an Iceberg table with Parquet data](#ctas-example-iceberg-parquet) 
+  [Example: Creating an Iceberg table with Avro data](#ctas-example-iceberg-avro) 

**Example: Duplicating a table by selecting all columns**  
The following example creates a table by copying all columns from a table:  

```
CREATE TABLE new_table AS 
SELECT * 
FROM old_table;
```
In the following variation of the same example, your `SELECT` statement also includes a `WHERE` clause\. In this case, the query selects only those rows from the table that satisfy the `WHERE` clause:   

```
CREATE TABLE new_table AS 
SELECT * 
FROM old_table 
WHERE condition;
```

**Example: Selecting specific columns from one or more tables**  
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

**Example: Creating an empty copy of an existing table**  
The following example uses `WITH NO DATA` to create a new table that is empty and has the same schema as the original table:  

```
CREATE TABLE new_table 
AS SELECT * 
FROM old_table
WITH NO DATA;
```

**Example: Specifying data storage and compression formats**  
With CTAS, you can use a source table in one storage format to create another table in a different storage format\.   
Use the `format` property to specify `ORC`, `PARQUET`, `AVRO`, `JSON`, or `TEXTFILE` as the storage format for the new table\.   
For the `PARQUET`, `ORC`, `TEXTFILE`, and `JSON` storage formats, use the `write_compression` property to specify the compression format for the new table's data\. For information about the compression formats that each file format supports, see [Athena compression support](compression-formats.md)\.  
The following example specifies that data in the table `new_table` be stored in Parquet format and use Snappy compression\. The default compression for Parquet is `GZIP`\.  

```
CREATE TABLE new_table
WITH (
      format = 'Parquet',
      write_compression = 'SNAPPY')
AS SELECT *
FROM old_table;
```
The following example specifies that data in the table `new_table` be stored in ORC format using Snappy compression\. The default compression for ORC is ZLIB\.  

```
CREATE TABLE new_table
WITH (format = 'ORC',
      write_compression = 'SNAPPY')
AS SELECT *
FROM old_table ;
```
The following example specifies that data in the table `new_table` be stored in textfile format using Snappy compression\. The default compression for both the textfile and JSON formats is GZIP\.  

```
CREATE TABLE new_table
WITH (format = 'TEXTFILE',
      write_compression = 'SNAPPY')
AS SELECT *
FROM old_table ;
```

**Example: Writing query results to a different format**  
The following CTAS query selects all records from `old_table`, which could be stored in CSV or another format, and creates a new table with underlying data saved to Amazon S3 in ORC format:   

```
CREATE TABLE my_orc_ctas_table
WITH (
      external_location = 's3://my_athena_results/my_orc_stas_table/',
      format = 'ORC')
AS SELECT * 
FROM old_table;
```

**Example: Creating unpartitioned tables**  
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

**Example: Creating partitioned tables**  
The following examples show `CREATE TABLE AS SELECT` queries for partitioned tables in different storage formats, using `partitioned_by`, and other properties in the `WITH` clause\. For syntax, see [CTAS table properties](create-table-as.md#ctas-table-properties)\. For more information about choosing the columns for partitioning, see [Partitioning and bucketing in Athena](ctas-partitioning-and-bucketing.md)\.  
List partition columns at the end of the list of columns in the `SELECT` statement\. You can partition by more than one column, and have up to 100 unique partition and bucket combinations\. For example, you can have 100 partitions if no buckets are specified\.

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

**Example: Creating bucketed and partitioned tables**  
The following example shows a `CREATE TABLE AS SELECT` query that uses both partitioning and bucketing for storing query results in Amazon S3\. The table results are partitioned and bucketed by different columns\. Athena supports a maximum of 100 unique bucket and partition combinations\. For example, if you create a table with five buckets, 20 partitions with five buckets each are supported\. For syntax, see [CTAS table properties](create-table-as.md#ctas-table-properties)\.  
For information about choosing the columns for bucketing, see [Partitioning and bucketing in Athena](ctas-partitioning-and-bucketing.md)\.  

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

**Example: Creating an Iceberg table with Parquet data**  
The following example creates an Iceberg table with Parquet data files\. The files are partitioned by month using the `dt` column in `table1`\. The example updates the retention properties on the table so that 10 snapshots are retained by default on every branch in the table\. Snapshots within the past 30 days are also retained\.  
  

```
CREATE TABLE ctas_iceberg_parquet
WITH (table_type = 'ICEBERG',
      format = 'PARQUET', 
      location = 's3://my_athena_results/ctas_iceberg_parquet/', 
      is_external = false,
      partitioning = ARRAY['month(dt)'],
      vacuum_min_snapshots_to_keep = 10,
      vacuum_max_snapshot_age_ms = 259200
   ) 
AS SELECT key1, name1, dt FROM table1;
```

**Example: Creating an Iceberg table with Avro data**  
The following example creates an Iceberg table with Avro data files partitioned by `key1`\.  

```
CREATE TABLE ctas_iceberg_avro
WITH ( format = 'AVRO', 
       location = 's3://my_athena_results/ctas_iceberg_avro/', 
       is_external = false,
       table_type = 'ICEBERG',
       partitioning = ARRAY['key1']) 
AS SELECT key1, name1, date FROM table1;
```