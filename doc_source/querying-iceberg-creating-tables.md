# Creating Iceberg Tables<a name="querying-iceberg-creating-tables"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

Athena creates Iceberg v2 tables\. For the difference between v1 and v2 tables, see [Format version changes](https://iceberg.apache.org/#spec/#appendix-e-format-version-changes) in the Apache Iceberg documentation\.

To create an Iceberg table from Athena, set the `'table_type'` table property to `'ICEBERG'` in the `TBL_PROPERTIES` clause, as in the following syntax summary\.

```
CREATE TABLE
  [db_name.]table_name (col_name data_type [COMMENT col_comment] [, ...] )
  [PARTITIONED BY (col_name | transform, ... )]
  LOCATION 's3://DOC-EXAMPLE-BUCKET/[your-folder]/'
  TBLPROPERTIES ( 'table_type' ='ICEBERG' [, property_name=property_value] )
```

**Note**  
The `EXTERNAL` keyword is not used in Athena to create Iceberg tables\. Using it results in the error message External keyword not supported for table type ICEBERG\.

## Partitioning<a name="querying-iceberg-partitioning"></a>

To create Iceberg tables with partitions, use `PARTITIONED BY` syntax\. Columns used for partitioning must be specified in the columns declarations first\. Within the `PARTITIONED BY` clause, the column type must not be included\. You can also define [partition transforms](https://iceberg.apache.org/#spec/#partition-transforms) in `CREATE TABLE` syntax\. To specify multiple columns for partitioning, separate the columns with the comma \(`,`\) character, as in the following example\.

```
PARTITIONED BY (category, bucket(16, id))
```

For more information about bucketing in Apache Hive, see [LanguageManual DDL BucketedTables](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL+BucketedTables) in the Apache Hive documentation\.

The following table shows the available partition transform functions\.


****  

| Function | Description | Supported types | 
| --- | --- | --- | 
| year\(ts\) | Partition by year | date, timestamp | 
| month\(ts\) | Partition by month | date, timestamp | 
| day\(ts\)  | Partition by day | date, timestamp | 
| hour\(ts\) | Partition by hour | timestamp | 
| bucket\(N, col\) | Partition by hashed value mod N buckets | int, long, decimal, date, timestamp, string, binary  | 
| truncate\(L, col\) | Partition by value truncated to L | int, long, decimal, string | 

Athena supports Iceberg's hidden partitioning\. For more information, see [Iceberg's hidden partitioning](https://iceberg.incubator.apache.org/partitioning/#icebergs-hidden-partitioning) in the Apache Iceberg documentation\.

## Table Properties<a name="querying-iceberg-table-properties"></a>

This section describes table properties that you can specify as key\-value pairs in the `TBLPROPERTIES` clause of the `CREATE TABLE` statement\.

Athena allows only a predefined list of key\-value pairs in the table properties for creating or altering Iceberg tables\. The following tables show the table properties that you can specify in preview\. For more information about the compaction options, see [Optimizing Iceberg Tables](querying-iceberg-data-optimization.md) later in this document\. If you would like Athena to support a specific open source table configuration property, send feedback to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\. 

***format***


****  

|  |  | 
| --- |--- |
| Description | File data format | 
| Allowed property values | parquet | 
| Default value | parquet | 

***write\_compression***


****  

|  |  | 
| --- |--- |
| Description | File compression codec | 
| Allowed property values | gzip | 
| Default value | gzip | 

***compaction\_bin\_pack\_target\_file\_size\_bytes***


****  

|  |  | 
| --- |--- |
| Description | Data optimization specific configuration\. Specifies the target size in bytes of the files generated\. | 
| Allowed property values | A positive number | 
| Default value | 536870912 \(512 MB\) | 

***compaction\_bin\_pack\_min\_file\_size\_bytes***


****  

|  |  | 
| --- |--- |
| Description | Data optimization specific configuration\. Files smaller than the specified value are included for compaction\. | 
| Allowed property values | A non\-negative number\. Must be less than 10GB and less than the value of compaction\_bin\_pack\_target\_file\_size\_bytes\. | 
| Default value | 0\.75 times the value of compaction\_bin\_pack\_target\_file\_size\_bytes | 

***compaction\_bin\_pack\_max\_file\_size\_bytes***


****  

|  |  | 
| --- |--- |
| Description | Data optimization specific configuration\. Files larger than the specified value are included for compaction\. | 
| Allowed property values | A positive number\. Must be less than 10GB and greater than the value of compaction\_bin\_pack\_target\_file\_size\_bytes\. | 
| Default value | 1\.8 times the value of compaction\_bin\_pack\_target\_file\_size\_bytes | 

## Example CREATE TABLE Statement<a name="querying-iceberg-example-create-table-statement"></a>

The following example creates an Iceberg table that has three columns\.

```
CREATE TABLE iceberg_table (
  id int,
  data string,
  category string) 
PARTITIONED BY (category, bucket(16,id)) 
LOCATION 's3://DOC-EXAMPLE-BUCKET/iceberg-folder' 
TBLPROPERTIES (
  'table_type'='ICEBERG',
  'format'='parquet',
  'compaction_bin_pack_target_file_size_bytes'='536870912' 
)
```