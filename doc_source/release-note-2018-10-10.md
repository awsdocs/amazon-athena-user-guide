# October 10, 2018<a name="release-note-2018-10-10"></a>

Published on *2018\-10\-10*

Athena supports `CREATE TABLE AS SELECT`, which creates a table from the result of a `SELECT` query statement\. For details, see [Creating a Table from Query Results \(CTAS\)](https://docs.aws.amazon.com/athena/latest/ug/ctas.html)\.

Before you create CTAS queries, it is important to learn about their behavior in the Athena documentation\. It contains information about the location for saving query results in Amazon S3, the list of supported formats for storing CTAS query results, the number of partitions you can create, and supported compression formats\. For more information, see [Considerations and Limitations for CTAS Queries](considerations-ctas.md)\.

Use CTAS queries to:
+ [Create a table from query results](ctas.md) in one step\.
+ [Create CTAS queries in the Athena console](ctas-console.md), using [Examples](ctas-examples.md)\. For information about syntax, see [CREATE TABLE AS](create-table-as.md)\.
+  Transform query results into other storage formats, such as PARQUET, ORC, AVRO, JSON, and TEXTFILE\. For more information, see [Considerations and Limitations for CTAS Queries](considerations-ctas.md) and [Columnar Storage Formats](columnar-storage.md)\.