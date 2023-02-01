# Considerations and limitations for CTAS queries<a name="ctas-considerations-limitations"></a>

The following sections describe considerations and limitations to keep in mind when you use `CREATE TABLE AS SELECT` \(CTAS\) queries in Athena\.

## CTAS query syntax<a name="ctas-considerations-limitations-query-syntax"></a>

The CTAS query syntax differs from the syntax of `CREATE [EXTERNAL] TABLE` used for creating tables\. See [ CREATE TABLE AS ](create-table-as.md)\.

**Note**  
Table, database, or column names for CTAS queries should not contain quotes or backticks\. To ensure this, check that your table, database, or column names do not represent [reserved words](reserved-words.md), and do not contain special characters \(which require enclosing them in quotes or backticks\)\. For more information, see [Names for tables, databases, and columns](tables-databases-columns-names.md)\.

## CTAS queries vs views<a name="ctas-considerations-limitations-queries-vs-views"></a>

CTAS queries write new data to a specified location in Amazon S3, whereas views do not write any data\. 

## Location of CTAS query results<a name="ctas-considerations-limitations-location-of-query-results"></a>

If your workgroup [overrides the client\-side setting](workgroups-settings-override.md) for query results location, Athena creates your table in the location `s3://<workgroup-query-results-location>/tables/<query-id>/`\. To see the query results location specified for the workgroup, [view the workgroup's details](workgroups-create-update-delete.md#viewing-details-workgroups)\.

If your workgroup does not override the query results location, you can use the syntax `WITH (external_location ='s3://location/')` in your CTAS query to specify where your CTAS query results are stored\. 

**Note**  
The `external_location` property must specify a location that is empty\. A CTAS query checks that the path location \(prefix\) in the bucket is empty and never overwrites the data if the location already has data in it\. To use the same location again, delete the data in the key prefix location in the bucket\.

If you omit the `external_location` syntax and are not using the workgroup setting, Athena uses your [client\-side setting](querying.md#query-results-specify-location-console) for the query results location and creates your table in the location `s3://<client-query-results-location>/<Unsaved-or-query-name>/<year>/<month/<date>/tables/<query-id>/`\. 

## Locating Orphaned Files<a name="ctas-considerations-limitations-locating-orphaned-files"></a>

If a `CTAS` or `INSERT INTO` statement fails, it is possible that orphaned data are left in the data location\. Because Athena does not delete any data \(even partial data\) from your bucket, you might be able to read this partial data in subsequent queries\. To locate orphaned files for inspection or deletion, you can use the data manifest file that Athena provides to track the list of files to be written\. For more information, see [Identifying query output files](querying.md#querying-identifying-output-files) and [DataManifestLocation](https://docs.aws.amazon.com/athena/latest/APIReference/API_QueryExecutionStatistics.html#athena-Type-QueryExecutionStatistics-DataManifestLocation)\.

## Formats for storing query results<a name="ctas-considerations-limitations-formats-for-query-results"></a>

The results of CTAS queries are stored in Parquet by default if you don't specify a data storage format\. You can store CTAS results in `PARQUET`, `ORC`, `AVRO`, `JSON`, and `TEXTFILE`\. Multi\-character delimiters are not supported for the CTAS `TEXTFILE` format\. CTAS queries do not require specifying a SerDe to interpret format transformations\. See [Example: Writing Query Results to a Different Format](ctas-examples.md#ctas-example-format)\.

## Compression formats<a name="ctas-considerations-limitations-compression-formats"></a>

`GZIP` compression is used for CTAS query results in JSON and TEXTFILE formats\. For Parquet, you can use `GZIP` or `SNAPPY`, and the default is `GZIP`\. For ORC, you can use `LZ4`, `SNAPPY`, `ZLIB`, or `ZSTD`, and the default is `ZLIB`\. For CTAS examples that specify compression, see [Example: Specifying Data Storage and Compression Formats](ctas-examples.md#ctas-example-compression)\. For more information about compression in Athena, see [Athena compression support](compression-formats.md)\.

## Partition and Bucket Limits<a name="ctas-considerations-limitations-partition-and-bucket-limits"></a>

You can partition and bucket the results data of a CTAS query\. For more information, see [Bucketing vs partitioning](ctas-bucketing-vs-partitioning.md)\. Athena supports writing to 100 unique partition and bucket combinations\. For example, if no buckets are defined in the destination table, you can specify a maximum of 100 partitions\. If you specify five buckets, 20 partitions \(each with five buckets\) are allowed\. If you exceed this count, an error occurs\.

Include partitioning and bucketing predicates at the end of the `WITH` clause that specifies properties of the destination table\. For more information, see [Example: Creating Bucketed and Partitioned Tables](ctas-examples.md#ctas-example-bucketed) and [Bucketing vs partitioning](ctas-bucketing-vs-partitioning.md)\.

For information about working around the 100\-partition limitation, see [Using CTAS and INSERT INTO to create a table with more than 100 partitions](ctas-insert-into.md)\.

## Encryption<a name="ctas-considerations-limitations-Encryption"></a>

You can encrypt CTAS query results in Amazon S3, similar to the way you encrypt other query results in Athena\. For more information, see [Encrypting Athena query results stored in Amazon S3Encrypting Athena query results when using JDBC or ODBC](encrypting-query-results-stored-in-s3.md)\.

## Expected bucket owner<a name="ctas-considerations-limitations-expected-bucket-owner"></a>

For CTAS statements, the expected bucket owner setting does not apply to the destination table location in Amazon S3\. The expected bucket owner setting applies only to the Amazon S3 output location that you specify for Athena query results\. For more information, see [Specifying a query result location using the Athena console](querying.md#query-results-specify-location-console)\.

## Data types<a name="ctas-considerations-limitations-data-types"></a>

Column data types for a CTAS query are the same as specified for the original query\.