# Columnar storage formats<a name="columnar-storage"></a>

[Apache parquet](https://parquet.apache.org) and [ORC](https://orc.apache.org/) are columnar storage formats that are optimized for fast retrieval of data and used in AWS analytical applications\.

Columnar storage formats have the following characteristics that make them suitable for using with Athena: 
+ *Compression by column, with compression algorithm selected for the column data type* to save storage space in Amazon S3 and reduce disk space and I/O during query processing\.
+ *Predicate pushdown* in Parquet and ORC enables Athena queries to fetch only the blocks it needs, improving query performance\. When an Athena query obtains specific column values from your data, it uses statistics from data block predicates, such as max/min values, to determine whether to read or skip the block\. 
+ *Splitting of data * in Parquet and ORC allows Athena to split the reading of data to multiple readers and increase parallelism during its query processing\. 

To convert your existing raw data from other storage formats to Parquet or ORC, you can run [CREATE TABLE AS SELECT \(CTAS\)](ctas.md) queries in Athena and specify a data storage format as Parquet or ORC, or use the AWS Glue Crawler\.