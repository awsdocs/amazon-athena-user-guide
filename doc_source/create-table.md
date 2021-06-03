# CREATE TABLE<a name="create-table"></a>

Creates a table with the name and the parameters that you specify\.

## Synopsis<a name="synopsis"></a>

```
CREATE EXTERNAL TABLE [IF NOT EXISTS]
 [db_name.]table_name [(col_name data_type [COMMENT col_comment] [, ...] )]
 [COMMENT table_comment]
 [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
 [CLUSTERED BY (col_name, col_name, ...) INTO num_buckets BUCKETS]
 [ROW FORMAT row_format]
 [STORED AS file_format] 
 [WITH SERDEPROPERTIES (...)]
 [LOCATION 's3://bucket_name/[folder]/']
 [TBLPROPERTIES ( ['has_encrypted_data'='true | false',] ['classification'='aws_glue_classification',] property_name=property_value [, ...] ) ]
```

## Parameters<a name="parameters"></a>

**EXTERNAL**  
Specifies that the table is based on an underlying data file that exists in Amazon S3, in the `LOCATION` that you specify\. All tables created in Athena, except for those created using [CTAS](create-table-as.md), must be `EXTERNAL`\. When you create an external table, the data referenced must comply with the default format or the format that you specify with the `ROW FORMAT`, `STORED AS`, and `WITH SERDEPROPERTIES` clauses\.

**\[IF NOT EXISTS\]**  
Causes the error message to be suppressed if a table named `table_name` already exists\.

**\[db\_name\.\]table\_name**  
Specifies a name for the table to be created\. The optional `db_name` parameter specifies the database where the table exists\. If omitted, the current database is assumed\. If the table name includes numbers, enclose `table_name` in quotation marks, for example `"table123"`\. If `table_name` begins with an underscore, use backticks, for example, ``_mytable``\. Special characters \(other than underscore\) are not supported\.  
Athena table names are case\-insensitive; however, if you work with Apache Spark, Spark requires lowercase table names\.

**\[ \( col\_name data\_type \[COMMENT col\_comment\] \[, \.\.\.\] \) \]**  
Specifies the name for each column to be created, along with the column's data type\. Column names do not allow special characters other than underscore `(_)`\. If `col_name` begins with an underscore, enclose the column name in backticks, for example ``_mycolumn``\.   
The `data_type` value can be any of the following:  
+ `BOOLEAN`\. Values are `true` and `false`\.
+ `TINYINT`\. A 8\-bit signed `INTEGER` in two’s complement format, with a minimum value of \-2^7 and a maximum value of 2^7\-1\.
+ `SMALLINT`\. A 16\-bit signed `INTEGER` in two’s complement format, with a minimum value of \-2^15 and a maximum value of 2^15\-1\.
+ `INT`\. Athena combines two different implementations of the `INTEGER` data type\. In Data Definition Language \(DDL\) queries, Athena uses the `INT` data type\. In all other queries, Athena uses the `INTEGER` data type, where `INTEGER` is represented as a 32\-bit signed value in two's complement format, with a minimum value of\-2^31 and a maximum value of 2^31\-1\. In the JDBC driver, `INTEGER` is returned, to ensure compatibility with business analytics applications\.
+ `BIGINT`\. A 64\-bit signed `INTEGER` in two’s complement format, with a minimum value of \-2^63 and a maximum value of 2^63\-1\.
+ `DOUBLE`
+ `FLOAT`
+ `DECIMAL [ (precision, scale) ]`, where `precision` is the total number of digits, and `scale` \(optional\) is the number of digits in fractional part, the default is 0\. For example, use these type definitions: `DECIMAL(11,5)`, `DECIMAL(15)`\. 

  To specify decimal values as literals, such as when selecting rows with a specific decimal value in a query DDL expression, specify the `DECIMAL` type definition, and list the decimal value as a literal \(in single quotes\) in your query, as in this example: `decimal_value = DECIMAL '0.12'`\.
+ `CHAR`\. Fixed length character data, with a specified length between 1 and 255, such as `char(10)`\. For more information, see [CHAR Hive Data Type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-char)\.
+ `VARCHAR`\. Variable length character data, with a specified length between 1 and 65535, such as `varchar(10)`\. For more information, see [VARCHAR Hive Data Type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-varchar)\. 
+ `STRING`\. A string literal enclosed in single or double quotes\.
**Note**  
Non\-string data types cannot be cast to `STRING` in Athena; cast them to `VARCHAR` instead\.
+ `BINARY` \(for data in Parquet\)
+ Date and time types
+ `DATE` A date in ISO format, such as `YYYY-MM-DD`\. For example, `DATE '2008-09-15'`\.
+ `TIMESTAMP` Date and time instant in a `java.sql.Timestamp` compatible format, such as `yyyy-MM-dd HH:mm:ss[.f...]`\. For example, `TIMESTAMP '2008-09-15 03:04:05.324'`\. This format uses the session time zone\.
+ `ARRAY` < data\_type >
+ `MAP` < primitive\_type, data\_type >
+ `STRUCT` < col\_name : data\_type \[COMMENT col\_comment\] \[, \.\.\.\] >

**\[COMMENT table\_comment\]**  
Creates the `comment` table property and populates it with the `table_comment` you specify\.

**\[PARTITIONED BY \(col\_name data\_type \[ COMMENT col\_comment \], \.\.\. \) \]**  
Creates a partitioned table with one or more partition columns that have the `col_name`, `data_type` and `col_comment` specified\. A table can have one or more partitions, which consist of a distinct column name and value combination\. A separate data directory is created for each specified combination, which can improve query performance in some circumstances\. Partitioned columns don't exist within the table data itself\. If you use a value for `col_name` that is the same as a table column, you get an error\. For more information, see [Partitioning Data](partitions.md)\.  
After you create a table with partitions, run a subsequent query that consists of the [MSCK REPAIR TABLE](msck-repair-table.md) clause to refresh partition metadata, for example, `MSCK REPAIR TABLE cloudfront_logs;`\. For partitions that are not Hive compatible, use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) to load the partitions so that you can query the data\.

**\[CLUSTERED BY \(col\_name, col\_name, \.\.\.\) INTO num\_buckets BUCKETS\]**  
Divides, with or without partitioning, the data in the specified `col_name` columns into data subsets called *buckets*\. The `num_buckets` parameter specifies the number of buckets to create\. Bucketing can improve the performance of some queries on large data sets\.

**\[ROW FORMAT row\_format\]**  
Specifies the row format of the table and its underlying source data if applicable\. For `row_format`, you can specify one or more delimiters with the `DELIMITED` clause or, alternatively, use the `SERDE` clause as described below\. If `ROW FORMAT` is omitted or `ROW FORMAT DELIMITED` is specified, a native SerDe is used\.  
+ \[DELIMITED FIELDS TERMINATED BY char \[ESCAPED BY char\]\]
+ \[DELIMITED COLLECTION ITEMS TERMINATED BY char\]
+ \[MAP KEYS TERMINATED BY char\]
+ \[LINES TERMINATED BY char\]
+ \[NULL DEFINED AS char\]

  Available only with Hive 0\.13 and when the STORED AS file format is `TEXTFILE`\.
 **\-\-OR\-\-**   
+ SERDE 'serde\_name' \[WITH SERDEPROPERTIES \("property\_name" = "property\_value", "property\_name" = "property\_value" \[, \.\.\.\] \)\]

  The `serde_name` indicates the SerDe to use\. The `WITH SERDEPROPERTIES` clause allows you to provide one or more custom properties allowed by the SerDe\.

**\[STORED AS file\_format\]**  
Specifies the file format for table data\. If omitted, `TEXTFILE` is the default\. Options for `file_format` are:  
+ SEQUENCEFILE
+ TEXTFILE
+ RCFILE
+ ORC
+ PARQUET
+ AVRO
+ INPUTFORMAT input\_format\_classname OUTPUTFORMAT output\_format\_classname

**\[LOCATION 's3://bucket\_name/\[folder\]/'\]**  
Specifies the location of the underlying data in Amazon S3 from which the table is created\. The location path must be a bucket name or a bucket name and one or more folders\. If you are using partitions, specify the root of the partitioned data\. For more information about table location, see [Table Location in Amazon S3](tables-location-format.md)\. For information about data format and permissions, see [Requirements for Tables in Athena and Data in Amazon S3](creating-tables.md#s3-considerations)\.   
Use a trailing slash for your folder or bucket\. Do not use file names or glob characters\.  
 **Use:**  
`s3://mybucket/`  
`s3://mybucket/folder/`  
`s3://mybucket/folder/anotherfolder/`  
 **Don't use:**  
`s3://path_to_bucket`  
`s3://path_to_bucket/*`  
`s3://path_to-bucket/mydatafile.dat`

**\[TBLPROPERTIES \( \['has\_encrypted\_data'='true \| false',\] \['classification'='aws\_glue\_classification',\] property\_name=property\_value \[, \.\.\.\] \) \]**  
Specifies custom metadata key\-value pairs for the table definition in addition to predefined table properties, such as `"comment"`\.  
Athena has a built\-in property, `has_encrypted_data`\. Set this property to `true` to indicate that the underlying dataset specified by `LOCATION` is encrypted\. If omitted and if the workgroup's settings do not override client\-side settings, `false` is assumed\. If omitted or set to `false` when underlying data is encrypted, the query results in an error\. For more information, see [Encryption at Rest](encryption.md)\.  
To run ETL jobs, AWS Glue requires that you create a table with the `classification` property to indicate the data type for AWS Glue as `csv`, `parquet`, `orc`, `avro`, or `json`\. For example, `'classification'='csv'`\. ETL jobs will fail if you do not specify this property\. You can subsequently specify it using the AWS Glue console, API, or CLI\. For more information, see [Using AWS Glue Jobs for ETL with Athena](glue-best-practices.md#schema-classifier) and [Authoring Jobs in Glue](https://docs.aws.amazon.com/glue/latest/dg/busisadd-job.html) in the *AWS Glue Developer Guide*\.

For more information about creating tables, see [Creating Tables in Athena](creating-tables.md)\.
