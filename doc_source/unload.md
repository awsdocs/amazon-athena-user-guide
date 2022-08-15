# UNLOAD<a name="unload"></a>

Writes query results from a `SELECT` statement to the specified data format\. Supported formats for `UNLOAD` include Apache Parquet, ORC, Apache Avro, and JSON\. CSV is the only output format used by the Athena `SELECT` query, but you can use `UNLOAD` to write the output of a `SELECT` query to the formats that `UNLOAD` supports\.

Although you can use the CTAS statement to output data in formats other than CSV, those statements also require the creation of a table in Athena\. The `UNLOAD` statement is useful when you want to output the results of a `SELECT` query in a non\-CSV format but do not require the associated table\. For example, a downstream application might require the results of a `SELECT` query to be in JSON format, and Parquet or ORC might provide a performance advantage over CSV if you intend to use the results of the `SELECT` query for additional analysis\.

## Considerations and limitations<a name="unload-considerations-and-limitations"></a>

When you use the `UNLOAD` statement in Athena, keep in mind the following points:
+ **Engine version** – Athena engine version 2 is required\.
+ **No global ordering of files** – `UNLOAD` results are written to multiple files in parallel\. If the `SELECT` query in the `UNLOAD` statement specifies a sort order, each file's contents are in sorted order, but the files are not sorted relative to each other\.
+ **Orphaned data not deleted** – In the case of a failure, Athena does not attempt to delete orphaned data\. This behavior is the same as that for CTAS and `INSERT INTO` statements\.
+ **Maximum partitions** – The maximum number of partitions that can be used with `UNLOAD` is 100\.
+ **Metadata and manifest files** – Athena generates a metadata file and data manifest file for each `UNLOAD` query\. The manifest tracks the files that the query wrote\. Both files are saved to your Athena query result location in Amazon S3\. For more information, see [Identifying query output files](querying.md#querying-identifying-output-files)\.
+ **Encryption** – `UNLOAD` output files are encrypted according to the encryption configuration used for Amazon S3\. To set up encryption configuration to encrypt your `UNLOAD` result, you can use the [EncryptionConfiguration API](https://docs.aws.amazon.com/athena/latest/APIReference/API_EncryptionConfiguration.html)\.
+ **Prepared statements** – `UNLOAD` can be used with prepared statements\. For information about prepared statements in Athena, see [Using parameterized queries](querying-with-prepared-statements.md)\.
+ **Service quotas** – UNLOAD uses DML query quotas\. For quota information, see [Service Quotas](service-limits.md)\.
+ **Expected bucket owner** – The expected bucket owner setting does not apply to the destination Amazon S3 location specfied in the `UNLOAD` query\. The expected bucket owner setting applies only to the Amazon S3 output location that you specify for Athena query results\. For more information, see [Specifying a query result location using the Athena console](querying.md#query-results-specify-location-console)\.

## Syntax<a name="unload-syntax"></a>

The `UNLOAD` statement uses the following syntax\.

```
UNLOAD (SELECT col_name[, ...] FROM old_table) 
TO 's3://my_athena_data_location/my_folder/' 
WITH ( property_name = 'expression' [, ...] )
```

**Note**  
The `TO` destination must specify a location in Amazon S3 that has no data\. Before the `UNLOAD` query writes to the location specified, it verifies that the bucket location is empty\. Because `UNLOAD` does not write data to the specified location if the location already has data in it, `UNLOAD` does not overwrite existing data\. To reuse a bucket location as a destination for `UNLOAD`, delete the data in the bucket location, and then run the query again\. 

### Parameters<a name="unload-parameters"></a>

Possible values for *property\_name* are as follows\.

** format = '*file\_format*' **  
Required\. Specifies the file format of the output\. Possible values for *file\_format* are `ORC`, `PARQUET`, `AVRO`, `JSON`, or `TEXTFILE`\.

** compression = '*compression\_format*' **  
Optional\. This option is specific to the ORC and Parquet formats\. For ORC, possible values are `lz4`, `snappy`, `zlib`, or `zstd`\. For Parquet, possible values are `gzip` or `snappy`\. For ORC, the default is `zlib`, and for Parquet, the default is `gzip`\.  
This option does not apply to the `AVRO` format\. Athena uses `gzip` for the `JSON` and `TEXTFILE` formats\.

** field\_delimiter = '*delimiter*' **  
Optional\. Specifies a single\-character field delimiter for files in CSV, TSV, and other text formats\. The following example specifies a comma delimiter\.  

```
WITH (field_delimiter = ',')
```
Currently, multicharacter field delimiters are not supported\. If you do not specify a field delimiter, the octal character `\001` \(^A\) is used\.

** partitioned\_by = ARRAY\[ *col\_name*\[,\.\.\.\] \] **  
Optional\. An array list of columns by which the output is partitioned\.  
 In your `SELECT` statement, make sure that the names of the partitioned columns are last in your list of columns\. 

## Examples<a name="unload-examples"></a>

The following example writes the output of a `SELECT` query to the Amazon S3 location `s3://DOC-EXAMPLE-BUCKET/unload_test_1/` using JSON format\.

```
UNLOAD (SELECT * FROM old_table) 
TO 's3://DOC-EXAMPLE-BUCKET/unload_test_1/' 
WITH (format = 'JSON')
```

The following example writes the output of a `SELECT` query in Parquet format using Snappy compression\.

```
UNLOAD (SELECT * FROM old_table) 
TO 's3://DOC-EXAMPLE-BUCKET/' 
WITH (format = 'PARQUET',compression = 'SNAPPY')
```

The following example writes four columns in text format, with the output partitioned by the last column\.

```
UNLOAD (SELECT name1, address1, comment1, key1 FROM table1) 
TO 's3://DOC-EXAMPLE-BUCKET/ partitioned/' 
WITH (format = 'TEXTFILE', partitioned_by = ARRAY['key1'])
```