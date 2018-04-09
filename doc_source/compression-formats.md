# Compression Formats<a name="compression-formats"></a>

Athena supports the following compression formats:
+ SNAPPY\. This is the default compression format for files in the Parquet format\. For Amazon Kinesis Firehose logs, SNAPPY compression is not supported\. Use GZIP instead\.
+ ZLIB\. This is the default compression format for files in the ORC format\.
+ GZIP
+ LZO