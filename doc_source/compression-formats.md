# Compression Formats<a name="compression-formats"></a>

Athena supports the following compression formats:

**Note**  
The compression formats listed in this section are used for [CREATE TABLE](create-table.md) queries\. For CTAS queries, Athena supports GZIP and SNAPPY \(for data stored in Parquet and ORC\)\. If you omit a format, GZIP is used by default\. For more information, see [CREATE TABLE AS](create-table-as.md)\.
+ SNAPPY\. This is the default compression format for files in the Parquet data storage format\. 
+ ZLIB\. This is the default compression format for files in the ORC data storage format\.
+ LZO
+ GZIP\. 

  For data in CSV, TSV, and JSON, Athena determines the compression type from the file extension\. If it is not present, the data is not decompressed\. If your data is compressed, make sure the file name includes the compression extension, such as `gz`\. 

  Use the GZIP compression in Athena for querying Amazon Kinesis Data Firehose logs\. Athena and Amazon Kinesis Data Firehose each support different versions of SNAPPY, so GZIP is the only compatible format\. 