# INSERT INTO<a name="insert-into"></a>

Inserts new rows into a destination table based on a `SELECT` query statement that runs on a source table, or based on a set of `VALUES` provided as part of the statement\. When the source table is based on underlying data in one format, such as CSV or JSON, and the destination table is based on another format, such as Parquet or ORC, you can use `INSERT INTO` queries to transform selected data into the destination table's format\. 

## Considerations and Limitations<a name="insert-into-limitations"></a>

Consider the following when using `INSERT` queries with Athena\.
+ When running an `INSERT` query on a table with underlying data that is encrypted in Amazon S3, the output files that the `INSERT` query writes are not encrypted by default\. We recommend that you encrypt `INSERT` query results if you are inserting into tables with encrypted data\. 

  For more information about encrypting query results using the console, see [Encrypting Athena Query Results Stored in Amazon S3Encrypting Athena query results when using JDBC or ODBC](encrypting-query-results-stored-in-s3.md)\. To enable encryption using the AWS CLI or Athena API, use the `EncryptionConfiguration` properties of the [StartQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_StartQueryExecution.html) action to specify Amazon S3 encryption options according to your requirements\.
+ For `INSERT INTO` statements, the expected bucket owner setting does not apply to the destination table location in Amazon S3\. The expected bucket owner setting applies only to the Amazon S3 output location that you specify for Athena query results\. For more information, see [Specifying a Query Result Location Using the Athena Console](querying.md#query-results-specify-location-console)\.

### Supported Formats and SerDes<a name="insert-into-supported-formats"></a>

You can run an `INSERT` query on tables created from data with the following formats and SerDes\.


| Data format | SerDe | 
| --- | --- | 
|  Avro  |  org\.apache\.hadoop\.hive\.serde2\.avro\.AvroSerDe  | 
|  JSON  |  org\.apache\.hive\.hcatalog\.data\.JsonSerDe  | 
|  ORC  |  org\.apache\.hadoop\.hive\.ql\.io\.orc\.OrcSerde  | 
|  Parquet  |  org\.apache\.hadoop\.hive\.ql\.io\.parquet\.serde\.ParquetHiveSerDe  | 
|  Text file  |  org\.apache\.hadoop\.hive\.serde2\.lazy\.LazySimpleSerDe  CSV, TSV, and custom\-delimited files are supported\.   | 

### Bucketed Tables Not Supported<a name="insert-into-bucketed-tables-not-supported"></a>

`INSERT INTO` is not supported on bucketed tables\. For more information, see [Bucketing vs Partitioning](bucketing-vs-partitioning.md)\.

### Federated Queries Not Supported<a name="insert-into-federated-queries-not-supported"></a>

`INSERT INTO` is not supported for federated queries\. Attempting to do so may result in the error message This operation is currently not supported for external catalogs\. For information about federated queries, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md)\.

### Partitioning<a name="insert-into-limitations-partitioning"></a>

Consider the points in this section when using parititioning with `INSERT INTO` or `CREATE TABLE AS SELECT` queries\.

#### Limits<a name="insert-into-partition-limits"></a>

The `INSERT INTO` statement supports writing a maximum of 100 partitions to the destination table\. If you run the `SELECT` clause on a table with more than 100 partitions, the query fails unless the `SELECT` query is limited to 100 partitions or fewer\.

For information about working around this limitation, see [Using CTAS and INSERT INTO to Create a Table with More Than 100 Partitions](ctas-insert-into.md)\.

#### Column Ordering<a name="insert-into-partition-detection"></a>

`INSERT INTO` or `CREATE TABLE AS SELECT` statements expect the partitioned column to be the last column in the list of projected columns in a `SELECT` statement\. 

If the source table is non\-partitioned, or partitioned on different columns compared to the destination table, queries like `INSERT INTO destination_table SELECT * FROM source_table` consider the values in the last column of the source table to be values for a partition column in the destination table\. Keep this in mind when trying to create a partitioned table from a non\-partitioned table\.

#### Resources<a name="insert-into-partition-resources"></a>

For more information about using `INSERT INTO` with partitioning, see the following resources\.
+ For inserting partitioned data into a partitioned table, see [Using CTAS and INSERT INTO to Create a Table with More Than 100 Partitions](ctas-insert-into.md)\.
+ For inserting unpartitioned data into a partitioned table, see [Using CTAS and INSERT INTO for ETL and Data Analysis](ctas-insert-into-etl.md)\. 

### Files Written to Amazon S3<a name="insert-into-files-written-to-s3"></a>

Athena writes files to source data locations in Amazon S3 as a result of the `INSERT` command\. Each `INSERT` operation creates a new file, rather than appending to an existing file\. The file locations depend on the structure of the table and the `SELECT` query, if present\. Athena generates a data manifest file for each `INSERT` query\. The manifest tracks the files that the query wrote\. It is saved to the Athena query result location in Amazon S3\. For more information, see [Identifying Query Output Files](querying.md#querying-identifying-output-files)\.

### Locating Orphaned Files<a name="insert-into-files-partial-data"></a>

If a `CTAS` or `INSERT INTO` statement fails, it is possible that orphaned data are left in the data location\. Because Athena does not delete any data \(even partial data\) from your bucket, you might be able to read this partial data in subsequent queries\. To locate orphaned files for inspection or deletion, you can use the data manifest file that Athena provides to track the list of files to be written\. For more information, see [Identifying Query Output Files](querying.md#querying-identifying-output-files) and [DataManifestLocation](https://docs.aws.amazon.com/athena/latest/APIReference/API_QueryExecutionStatistics.html#athena-Type-QueryExecutionStatistics-DataManifestLocation)\.

## INSERT INTO\.\.\.SELECT<a name="insert-into-select"></a>

Specifies the query to run on one table, `source_table`, which determines rows to insert into a second table, `destination_table`\. If the `SELECT` query specifies columns in the `source_table`, the columns must precisely match those in the `destination_table`\.

For more information about `SELECT` queries, see [SELECT](select.md)\.

### Synopsis<a name="insert-into-select-synopsis"></a>

```
INSERT INTO destination_table 
SELECT select_query 
FROM source_table_or_view
```

### Examples<a name="insert-into-select-examples"></a>

Select all rows in the `vancouver_pageviews` table and insert them into the `canada_pageviews` table:

```
INSERT INTO canada_pageviews 
SELECT * 
FROM vancouver_pageviews;
```

Select only those rows in the `vancouver_pageviews` table where the `date` column has a value between `2019-07-01` and `2019-07-31`, and then insert them into `canada_july_pageviews`:

```
INSERT INTO canada_july_pageviews
SELECT *
FROM vancouver_pageviews
WHERE date
    BETWEEN date '2019-07-01'
        AND '2019-07-31';
```

Select the values in the `city` and `state` columns in the `cities_world` table only from those rows with a value of `usa` in the `country` column and insert them into the `city` and `state` columns in the `cities_usa` table:

```
INSERT INTO cities_usa (city,state)
SELECT city,state
FROM cities_world
    WHERE country='usa'
```

## INSERT INTO\.\.\.VALUES<a name="insert-into-values"></a>

Inserts rows into an existing table by specifying columns and values\. Specified columns and associated data types must precisely match the columns and data types in the destination table\.

**Important**  
We do not recommend inserting rows using `VALUES` because Athena generates files for each `INSERT` operation\. This can cause many small files to be created and degrade the table's query performance\. To identify files that an `INSERT` query creates, examine the data manifest file\. For more information, see [Working with Query Results, Recent Queries, and Output Files](querying.md)\.

### Synopsis<a name="insert-into-values-synopsis"></a>

```
INSERT INTO destination_table [(col1,col2,...)] 
VALUES (col1value,col2value,...)[,
       (col1value,col2value,...)][,
       ...]
```

### Examples<a name="insert-into-values-examples"></a>

In the following examples, the cities table has three columns: `id`, `city`, `state`, `state_motto`\. The `id` column is type `INT` and all other columns are type `VARCHAR`\.

Insert a single row into the `cities` table, with all column values specified:

```
INSERT INTO cities 
VALUES (1,'Lansing','MI','Si quaeris peninsulam amoenam circumspice')
```

Insert two rows into the `cities` table:

```
INSERT INTO cities 
VALUES (1,'Lansing','MI','Si quaeris peninsulam amoenam circumspice'),
       (3,'Boise','ID','Esto perpetua')
```