# Supported data and storage formats<a name="notebooks-spark-data-and-storage-formats"></a>

The following table shows formats that are supported natively\. For more information about Spark data sources, see [Data Sources](https://spark.apache.org/docs/3.2.1/sql-data-sources.html) in the Apache Spark documentation\.


****  

| **Data format** | **Read** | **Write** | **Write compression** | 
| --- | --- | --- | --- | 
| parquet | yes | yes | none, uncompressed, snappy, gzip | 
| orc | yes | yes | none, snappy, zlib, lzo | 
| json | yes | yes | bzip2, gzip, deflate | 
| csv | yes | yes | bzip2, gzip, deflate | 
| text | yes | yes | none, bzip2, gzip, deflate | 
| binary file | yes | N/A | N/A | 