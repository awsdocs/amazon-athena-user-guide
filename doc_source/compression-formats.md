# Athena compression support<a name="compression-formats"></a>

Athena supports a variety of compression formats for reading and writing data, including reading from a table that uses multiple compression formats\. For example, Athena can successfully read the data in a table that uses Parquet file format when some Parquet files are compressed with Snappy and other Parquet files are compressed with GZIP\. The same principle applies for ORC, text file, and JSON storage formats\.

Athena supports the following compression formats:
+ **BZIP2** – Format that uses the Burrows\-Wheeler algorithm\.
+ **DEFLATE** – Compression algorithm based on [LZSS](https://en.wikipedia.org/wiki/Lempel%E2%80%93Ziv%E2%80%93Storer%E2%80%93Szymanski) and [Huffman coding](https://en.wikipedia.org/wiki/Huffman_coding)\. [Deflate](https://en.wikipedia.org/wiki/Deflate) is relevant only for the Avro file format\.
+ **GZIP** – Compression algorithm based on Deflate\. GZIP is the default write compression format for files in the Parquet and text file storage formats\. Files in the `tar.gz` format are not supported\.
+ **LZ4** – This member of the Lempel\-Ziv 77 \(LZ7\) family also focuses on compression and decompression speed rather than maximum compression of data\. LZ4 has the following framing formats:
  + **LZ4 Raw/Unframed** – An unframed, standard implementation of the LZ4 block compression format\. For more information, see the [LZ4 block format description](https://github.com/lz4/lz4/blob/dev/doc/lz4_Block_format.md) on GitHub\.
  + **LZ4 framed** – The usual framing implementation of LZ4\. For more information, see the [LZ4 frame format description](https://github.com/lz4/lz4/blob/dev/doc/lz4_Frame_format.md) on GitHub\.
  + **LZ4 hadoop\-compatible** – The Apache Hadoop implementation of LZ4\. This implementation wraps LZ4 compression with the [BlockCompressorStream\.java](https://github.com/apache/hadoop/blob/f67237cbe7bc48a1b9088e990800b37529f1db2a/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/io/compress/BlockCompressorStream.java) class\.
+ **LZO** – Format that uses the Lempel–Ziv–Oberhumer algorithm, which focuses on high compression and decompression speed rather than the maximum compression of data\. LZO has two implementations:
  + **Standard LZO** – For more information, see the LZO [abstract](http://www.oberhumer.com/opensource/lzo/#abstract) on the Oberhumer website\.
  + **LZO hadoop\-compatible** – This implementation wraps the LZO algorithm with the [BlockCompressorStream\.java](https://github.com/apache/hadoop/blob/f67237cbe7bc48a1b9088e990800b37529f1db2a/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/io/compress/BlockCompressorStream.java) class\.
+ **SNAPPY** – Compression algorithm that is part of the Lempel\-Ziv 77 \(LZ7\) family\. Snappy focuses on high compression and decompression speed rather than the maximum compression of data\.

  Some implementations of Snappy allow for framing\. Framing enables decompression of streaming or file data that cannot be entirely maintained in memory\. The following framing implementations are relevant for Athena:
  + **Snappy Raw/Unframed** – The standard implementation of the Snappy format that does not use framing\. For more information, see the [Snappy format description](https://github.com/google/snappy/blob/master/format_description.txt) on GitHub\.
  + **Snappy\-framed** – The framing implementation of the Snappy format\. For more information, see the [Snappy framing format](https://github.com/google/snappy/blob/master/framing_format.txt) description on GitHub\.
  + **Snappy hadoop\-compatible** – The framing implementation of Snappy that the Apache Hadoop Project uses\. For more information, see [BlockCompressorStream\.java](https://github.com/apache/hadoop/blob/f67237cbe7bc48a1b9088e990800b37529f1db2a/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/io/compress/BlockCompressorStream.java) on GitHub\.

  For information about the Snappy framing methods that Athena supports for each file format, see the table later on this page\.
+ **ZLIB** – Based on Deflate, ZLIB is the default write compression format for files in the ORC data storage format\. For more information, see the [zlib](https://github.com/madler/zlib) page on GitHub\.
+  **ZSTD** – The [Zstandard real\-time data compression algorithm](http://facebook.github.io/zstd/) is a fast compression algorithm that provides high compression ratios\. The Zstandard library is provided as open source software using a BSD license\. Athena supports reading and writing ZStandard compressed ORC, Parquet, and text file data\. When writing ZStandard compressed data, Athena uses ZStandard compression level 3\. 

## Compression support in Athena by file format<a name="compression-support-by-file-format"></a>

Compression support in Athena depends on the engine version\.

### Compression support in Athena engine version 3<a name="compression-support-by-file-format-v3"></a>

The following table summarizes the compression format support in Athena engine version 3 for each storage file format\. Text file format includes TSV, CSV, JSON, and custom SerDes for text\. "Yes" or "No" in a cell apply equally to read and write operations except where noted\. For the purposes of this table, CREATE TABLE, CTAS, and INSERT INTO are considered write operations\.


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

### Compression support in Athena engine version 2<a name="compression-support-by-file-format-v2"></a>

The following table summarizes the compression format support in Athena engine version 2\. Text file format includes TSV, CSV, JSON, and custom SerDes for text\. "Yes" or "No" in a cell apply equally to read and write operations except where noted\. For the purposes of this table, CREATE TABLE, CTAS, and INSERT INTO are considered write operations\.


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

## Specifying compression formats<a name="compression-support-specifying-compression-formats"></a>

When you write CREATE TABLE or CTAS statements, you can specify compression properties that specify the compression type to use when Athena writes to those tables\.
+ For CTAS, see [CTAS table properties](create-table-as.md#ctas-table-properties)\. For examples, see [Examples of CTAS queries](ctas-examples.md)\.
+ For CREATE TABLE, see [ALTER TABLE SET TBLPROPERTIES](alter-table-set-tblproperties.md) for a list of compression table properties\.

## Specifying no compression<a name="compression-support-specifying-no-compression"></a>

CREATE TABLE statements support writing uncompressed files\. To write uncompressed files, use the following syntax: 
+ CREATE TABLE \(text file or JSON\) – In `TBLPROPERTIES`, specify `write.compression = NONE`\.
+ CREATE TABLE \(Parquet\) – In `TBLPROPERTIES`, specify `parquet.compression = UNCOMPRESSED`\.
+ CREATE TABLE \(ORC\) – In `TBLPROPERTIES`, specify `orc.compress = NONE`\.

## Notes and resources<a name="compression-support-notes-and-resources"></a>
+ Currently, uppercase file extensions such as `.GZ` or `.BZIP2` are not recognized by Athena\. Avoid using datasets with uppercase file extensions, or rename the data file extensions to lowercase\.
+ For data in CSV, TSV, and JSON, Athena determines the compression type from the file extension\. If no file extension is present, Athena treats the data as uncompressed plain text\. If your data is compressed, make sure the file name includes the compression extension, such as `gz`\.
+ The ZIP file format is not supported\.
+ For querying Amazon Kinesis Data Firehose logs from Athena, supported formats include GZIP compression or ORC files with SNAPPY compression\.
+ For more information about using compression, see section 3 \("Compress and split files"\) of the AWS Big Data Blog post [Top 10 performance tuning tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.