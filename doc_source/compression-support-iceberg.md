# Iceberg table compression support by file format<a name="compression-support-iceberg"></a>

Apache Iceberg compression support in Athena depends on the engine version\.

## Iceberg compression support in Athena engine version 3<a name="compression-support-iceberg-v3"></a>

The following table summarizes the compression format support in Athena engine version 3 for storage file formats in Apache Iceberg\. Text file format includes TSV, CSV, JSON, and custom SerDes for text\. "Yes" or "No" in a cell apply equally to read and write operations except where noted\. For the purposes of this table, CREATE TABLE, CTAS, and INSERT INTO are considered write operations\. For more information about using ZSTD compression levels in Athena, see [Using ZSTD compression levels in Athena](compression-support-zstd-levels.md)\.


****  

|  | Avro | ORC | Parquet | 
| --- | --- | --- | --- | 
| BZIP2 | No | No | No | 
| GZIP | Yes | No | Yes | 
| LZ4 | No | Yes | No | 
| SNAPPY | Yes | Yes | Yes | 
| ZLIB | No | Yes | No | 
| ZSTD | Yes | Yes | Yes | 
| NONE | Yes \(specify None or Deflate\) | Yes | Yes \(specify None or Uncompressed\) | 

## Iceberg compression support in Athena engine version 2<a name="compression-support-iceberg-v2"></a>

The following table summarizes the compression format support in Athena engine version 2 for Apache Iceberg\. Text file format includes TSV, CSV, JSON, and custom SerDes for text\. "Yes" or "No" in a cell apply equally to read and write operations except where noted\. For the purposes of this table, CREATE TABLE, CTAS, and INSERT INTO are considered write operations\.


****  

|  | Avro\(Not supported\) | ORC\(Not supported\) | Parquet | 
| --- | --- | --- | --- | 
| BZIP2 | No | No | No | 
| GZIP | No | No | Yes | 
| LZ4 | No | No | No | 
| SNAPPY | No | No | Yes | 
| ZLIB | No | No | No | 
| ZSTD | No | No | Yes | 
| NONE | No | No | Yes | 