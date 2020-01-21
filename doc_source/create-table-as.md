# CREATE TABLE AS<a name="create-table-as"></a>

Creates a new table populated with the results of a [SELECT](select.md) query\. To create an empty table, use [CREATE TABLE](create-table.md)\.

**Topics**
+ [Synopsis](#synopsis)
+ [CTAS Table Properties](#ctas-table-properties)
+ [Examples](#ctas-table-examples)

## Synopsis<a name="synopsis"></a>

```
CREATE TABLE table_name
[ WITH ( property_name = expression [, ...] ) ]
AS query
[ WITH [ NO ] DATA ]
```

Where:

**WITH \( property\_name = expression \[, \.\.\.\] \)**  
A list of optional CTAS table properties, some of which are specific to the data storage format\. See [CTAS Table Properties](#ctas-table-properties)\.

**query**  
A [SELECT](select.md) query that is used to create a new table\.  
If you plan to create a query with partitions, specify the names of partitioned columns last in the list of columns in the `SELECT` statement\.

**\[ WITH \[ NO \] DATA \]**  
If `WITH NO DATA` is used, a new empty table with the same schema as the original table is created\.

## CTAS Table Properties<a name="ctas-table-properties"></a>

Each CTAS table in Athena has a list of optional CTAS table properties that you specify using `WITH (property_name = expression [, ...] )`\. For information about using these parameters, see [Examples of CTAS Queries](ctas-examples.md)\.

**`WITH (property_name = expression [, ...], )`**    
`external_location = [location]`   
The location where Athena saves your CTAS query in Amazon S3, for example, ` WITH (external_location ='s3://my-bucket/tables/parquet_table/')`\. This property is optional\. When you don’t specify any location and your workgroup does not [override client\-side settings](workgroups-settings-override.md), Athena stores the CTAS query results in `external_location = 's3://aws-athena-query-results-<account>-<region>/<query-name-or-unsaved>/<year>/<month>/<date>/<query-id>/'`, and does not use the same path again\. If you specify the location manually, make sure that the Amazon S3 location has no data\. Athena never attempts to delete your data\. If you want to use the same location again, manually clean the data, otherwise your CTAS query will fail\.  
If the workgroup in which a query will run is configured with an [enforced query results location](workgroups-settings-override.md), do not specify an `external_location` for the CTAS query\. Athena issues an error and fails a query that specifies an `external_location` in this case\. For example, this query fails, if you enforce the workgroup to use its own location: `CREATE TABLE <DB>.<TABLE1> WITH (format='Parquet', external_location='s3://my_test/test/') AS SELECT * FROM <DB>.<TABLE2> LIMIT 10;`  
To obtain the results location specified for the workgroup, [view workgroup's details](workgroups-create-update-delete.md#viewing-details-workgroups)\.  
`format = [format]`  
The data format for the CTAS query results, such as `ORC`, `PARQUET`, `AVRO`, `JSON`, or `TEXTFILE`\. For example, `WITH (format = 'PARQUET')`\. If omitted, `PARQUET` is used by default\. The name of this parameter, `format`, must be listed in lowercase, or your CTAS query will fail\.  
`partitioned_by = ( [col_name,…])`  
Optional\. An array list of columns by which the CTAS table will be partitioned\. Verify that the names of partitioned columns are listed last in the list of columns in the `SELECT` statement\.   
`bucketed_by( [bucket_name,…])`  
An array list of buckets to bucket data\. If omitted, Athena does not bucket your data in this query\.  
`bucket_count = [int]`  
The number of buckets for bucketing your data\. If omitted, Athena does not bucket your data\.  
`orc_compression = [format]`  
The compression type to use for ORC data\. For example, `WITH (orc_compression = 'ZLIB')`\. If omitted, GZIP compression is used by default for ORC and other data storage formats supported by CTAS\.  
`parquet_compression = [format]`  
The compression type to use for Parquet data\. For example, `WITH (parquet_compression = 'SNAPPY')`\. If omitted, GZIP compression is used by default for Parquet and other data storage formats supported by CTAS\.  
`field_delimiter = [delimiter]`  
Optional and specific to text\-based data storage formats\. The field delimiter for files in CSV, TSV, and text files\. For example, `WITH (field_delimiter = ',')`\. If you don't specify a field delimiter, `\001` is used by default\.

## Examples<a name="ctas-table-examples"></a>

See [Examples of CTAS Queries](ctas-examples.md)\.