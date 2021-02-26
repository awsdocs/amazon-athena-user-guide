# Compression Formats<a name="compression-formats"></a>

The compression formats listed in this section are used for [CREATE TABLE](create-table.md) queries\. For CTAS queries, Athena supports GZIP and SNAPPY \(for data stored in Parquet and ORC\)\. If you omit a format, GZIP is used by default\. For more information, see [CREATE TABLE AS](create-table-as.md)\.

Athena supports the following compression formats:
+ **`SNAPPY`** – The default compression format for files in the Parquet data storage format\. 
+ **`ZLIB`** – The default compression format for files in the ORC data storage format\.
+ **`LZO`** – Format that uses the Lempel–Ziv–Oberhumer algorithm\.
+ **`GZIP`** – Athena can query data in this format directly if the data files have the `.gz` extension\. No special directive is required in the `CREATE TABLE` statement\.
+ **`BZIP2`** – Format that uses the Burrows\-Wheeler algorithm\.
**Note**  
In rare cases, a known issue in Athena engine version 1 can cause records to be silently dropped when the `BZIP2` format is used\. For this reason, use of the `BZIP2` format in Athena engine version 1 is not recommended\. 

## Specifying Compression Formats<a name="compression-formats-specifying"></a>

To specify a compression format for the [Parquet SerDe](parquet-serde.md) or [ORC SerDe](orc-serde.md) in a `CREATE TABLE` statement , use the [TBLPROPERTIES](alter-table-set-tblproperties.md) clause\. To specify a compression format for Parquet or ORC in a [CTAS](ctas.md) statement, use the `WITH` clause\. For more information, see [CTAS Table Properties](create-table-as.md#ctas-table-properties) and [](ctas-examples.md#ctas-example-compression)\.

## Notes and Resources<a name="compression-formats-notes"></a>
+ For data in CSV, TSV, and JSON, Athena determines the compression type from the file extension\. If no file extension is present, Athena treats the data as uncompressed plain text\. If your data is compressed, make sure the file name includes the compression extension, such as `gz`\.
+ The ZIP file format is not supported\. 
+ For querying Amazon Kinesis Data Firehose logs from Athena, supported formats include GZIP compression or ORC files with SNAPPY compression\. 
+ For more information on using compression, see section 3 \("Compress and split files"\) of the AWS Big Data Blog post [Top 10 Performance Tuning Tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.