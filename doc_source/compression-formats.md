# Compression Formats<a name="compression-formats"></a>

Athena supports the following compression formats:
+ SNAPPY \(Default compression format for files in the Parquet format\) 
+ ZLIB\. \(Default compression format for files in the ORC format\)
+ GZIP
+ LZO
**Note**  
Use the GZIP compression in Athena for querying Amazon Kinesis Data Firehose logs\. Athena and Amazon Kinesis Data Firehose each support different versions of SNAPPY, so GZIP is the only compatible format\. 