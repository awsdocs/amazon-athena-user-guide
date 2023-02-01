# Hive table compression support by file format<a name="compression-support-hive"></a>

Hive compression support in Athena depends on the engine version\.

## Hive compression support in Athena engine version 3<a name="compression-support-hive-v3"></a>

The following table summarizes the compression format support in Athena engine version 3 for storage file formats in Apache Hive\. Text file format includes TSV, CSV, JSON, and custom SerDes for text\. "Yes" or "No" in a cell apply equally to read and write operations except where noted\. For the purposes of this table, CREATE TABLE, CTAS, and INSERT INTO are considered write operations\. For more information about using ZSTD compression levels in Athena, see [Using ZSTD compression levels in Athena](compression-support-zstd-levels.md)\.


****  

|  | Avro | Ion | ORC | Parquet | Text file | 
| --- | --- | --- | --- | --- | --- | 
| BZIP2 | Yes | Yes | No | No | Yes | 
| DEFLATE | Yes | No | No | No | No | 
| GZIP | No | Yes | No | Yes | Yes | 
| LZ4 | No | Yes | Yes | Yes | Yes | 
| LZO | No |  Write \- No Read \- Yes  | No | Yes |  Write \- No Read \- Yes  | 
| SNAPPY | Yes | Yes | Yes | Yes | Yes | 
| ZLIB | No | No | Yes | No | No | 
| ZSTD | Yes | Yes | Yes | Yes | Yes | 
| NONE | Yes | Yes | Yes | Yes | Yes | 

## Hive compression support in Athena engine version 2<a name="compression-support-hive-v2"></a>

The following table summarizes the compression format support in Athena engine version 2 for Apache Hive\. Text file format includes TSV, CSV, JSON, and custom SerDes for text\. "Yes" or "No" in a cell apply equally to read and write operations except where noted\. For the purposes of this table, CREATE TABLE, CTAS, and INSERT INTO are considered write operations\.


****  

|  | Avro | Ion | ORC | Parquet | Text file | 
| --- | --- | --- | --- | --- | --- | 
| BZIP2 | Yes | Yes | No | No | Yes | 
| DEFLATE | Yes | No | No | No | No | 
| GZIP | No | Yes | No | Yes | Yes | 
| LZ4 | No | No | Yes |  Write \- Yes Read \- No  |  Write \- No Read \- Yes  | 
| LZO | No |  Write \- No Read \- Yes  | No | Yes |  Write \- No Read \- Yes  | 
| SNAPPY | Yes | Yes | Yes | Yes | Yes | 
| ZLIB | No | No | Yes | No | No | 
| ZSTD | No | Yes | Yes | Yes | Yes | 
| NONE | Yes | Yes | Yes | Yes | Yes | 