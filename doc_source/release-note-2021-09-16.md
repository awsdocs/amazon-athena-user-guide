# September 16, 2021<a name="release-note-2021-09-16"></a>

Published on 2021\-09\-16

Athena announces the following new features and improvements\.

## Features<a name="release-note-2021-09-16-features"></a>
+ The Apache Hudi Metadata Listing Feature is now available for Hudi tables, reducing Amazon S3 overhead and query times for partitioned table queries\. For information about using Apache Hudi in Athena, see [Using Athena to Query Apache Hudi Datasets](querying-hudi.md)\.
+ Added support for specifying textfile and JSON compression in CTAS using the `write_compression` table property\. You can also specify the `write_compression` property in CTAS for the Parquet and ORC formats\. For more information, see [CTAS Table Properties](create-table-as.md#ctas-table-properties)\.
+ The BZIP2 compression format is now supported for writing textfile and JSON files\. For more information about the compression formats in Athena, see [Athena Compression Support](compression-formats.md)\.
+ The SQL `OFFSET` clause now supported in SELECT statements\. For more information, see [SELECT](select.md)\.

## Improvements<a name="release-note-2021-09-16-improvements"></a>
+ Fixed a bug in which identity information failed to be sent to the UDF Lambda function\.
+ Fixed a predicate pushdown issue with disjunct filter conditions\.
+ Fixed a hashing issue for decimal types\.
+ Fixed an unnecessary statistics collection issue\.
+ Removed an inconsistent error message\.
+ Improved broadcast join performance by applying dynamic partition pruning in the worker node\.
+ For federated queries:
  + Added date and timestamp support for the [Federated JDBC connector](https://docs.aws.amazon.com/athena/latest/ug/athena-prebuilt-data-connectors-jdbc.html)\.
  + Columns containing unsupported datatypes no longer get dropped in the Federated JDBC connector\.
  + Altered configuration to reduce the occurrence of `CONSTRAINT_VIOLATION` errors in federated queries\.