# Data types in Amazon Athena<a name="data-types"></a>

When you run [CREATE TABLE](create-table.md), you specify column names and the data type that each column can contain\. Athena supports the data types listed below\. For information about the data type mappings that the JDBC driver supports between Athena, JDBC, and Java, see [Data types](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf#page=37) in the *JDBC Driver Installation and Configuration Guide*\. For information about the data type mappings that the ODBC driver supports between Athena and SQL, see [Data types](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf#page=50) in the *ODBC Driver Installation and Configuration Guide*\.
+ **`boolean`** – Values are `true` and `false`\.
+ **`tinyint`** – A 8\-bit signed integer in two's complement format, with a minimum value of \-27 and a maximum value of 27\-1\.
+ **`smallint`** – A 16\-bit signed integer in two's complement format, with a minimum value of \-215 and a maximum value of 215\-1\.
+ **`int`** and **`integer`** – Athena uses different expressions for integer depending on the type of query\.
  + **`int`** – In Data Definition Language \(DDL\) queries like `CREATE TABLE`, use the `int` data type\.
  + **`integer`** – In DML queries like `SELECT * FROM`, use the `integer` data type\. `integer` is represented as a 32\-bit signed value in two's complement format, with a minimum value of \-231 and a maximum value of 231\-1\. 
    + To ensure compatibility with business analytics applications, the JDBC driver returns the `integer` type\.
+ **`bigint`** – A 64\-bit signed integer in two's complement format, with a minimum value of \-263 and a maximum value of 263\-1\.
+ **`double`** – A 64\-bit signed double\-precision floating point number\. The range is 4\.94065645841246544e\-324d to 1\.79769313486231570e\+308d, positive or negative\. `double` follows the IEEE Standard for Floating\-Point Arithmetic \(IEEE 754\)\.
+ **`float`** – A 32\-bit signed single\-precision floating point number\. The range is 1\.40129846432481707e\-45 to 3\.40282346638528860e\+38, positive or negative\. `float` follows the IEEE Standard for Floating\-Point Arithmetic \(IEEE 754\)\. Equivalent to the `real` in Presto\. In Athena, use `float` in DDL statements like `CREATE TABLE` and `real` in SQL functions like `SELECT CAST`\. The AWS Glue crawler returns values in `float`, and Athena translates `real` and `float` types internally \(see the [June 5, 2018](release-notes.md#release-note-2018-06-05) release notes\)\.
+ **`decimal`**`(precision, scale)` – `precision` is the total number of digits\. `scale` \(optional\) is the number of digits in fractional part with a default of 0\. For example, use these type definitions: `decimal(11,5)`, `decimal(15)`\. The maximum value for *precision* is 38, and the maximum value for *scale* is 38\.

  To specify decimal values as literals, such as when selecting rows with a specific decimal value in a query DDL expression, specify the `decimal` type definition, and list the decimal value as a literal \(in single quotes\) in your query, as in this example: `decimal_value = decimal '0.12'`\. 
+ **`char`** – Fixed length character data, with a specified length between 1 and 255, such as `char(10)`\. For more information, see [CHAR Hive data type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-char)\.
**Note**  
To use the `substr` function to return a substring of specified length from a `char` data type, you must first cast the `char` value as a `varchar`, as in the following example\.  

  ```
  substr(cast(col1 as varchar), 1, 4)
  ```
+ **`varchar`** – Variable length character data, with a specified length between 1 and 65535, such as `varchar(10)`\. For more information, see [VARCHAR Hive data type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-varchar)\. 
+ **`string`** – A string literal enclosed in single or double quotes\. For more information, see [STRING Hive data type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-StringsstringStrings)\.
**Note**  
Non\-string data types cannot be cast to `string` in Athena; cast them to `varchar` instead\.
+ **`binary`** – Used for data in Parquet\.
+ **`date`** – A date in ISO format, such as `YYYY-MM-DD`\. For example, `date '2008-09-15'`\. An exception is the OpenCSVSerDe, which uses the number of days elapsed since January 1, 1970\. For more information, see [OpenCSVSerDe for processing CSV](csv-serde.md)\.
+ **`timestamp`** – Date and time instant in a [https://docs.oracle.com/javase/8/docs/api/java/sql/Timestamp.html](https://docs.oracle.com/javase/8/docs/api/java/sql/Timestamp.html) compatible format up to a maximum resolution of milliseconds, such as `yyyy-MM-dd HH:mm:ss[.f...]`\. For example, `timestamp '2008-09-15 03:04:05.324'`\. An exception is the [OpenCSVSerDe](csv-serde.md), which uses `timestamp` data in the UNIX numeric format \(for example, `1579059880000`\)\. 

  For more information about working with timestamps, see [Working with timestamp data](#data-types-timestamps) later in this document\.
+ **`array`**`<data_type>` – An array of the given component type\.

  Example

  ```
  CREATE TABLE table array_table (c1 array<integer>) LOCATION '...';
  INSERT INTO array_table values(ARRAY[1,2,3]);
  ```
+ **`map`**`<primitive_type, data_type>` – A map between the given component types\.

  Example

  ```
  CREATE TABLE map_table(c1 map<string, integer>) LOCATION '...';
  INSERT INTO map_table values(MAP(ARRAY['foo', 'bar'], ARRAY[1, 2]));
  ```
+ **`struct`**`<col_name : data_type [comment col_comment] , ...>` – A collection of elements of different component types\.

  Example

  ```
  CREATE TABLE struct_table(c1 struct<name:varchar(10), age:integer>) LOCATION '...';
  INSERT INTO struct_table SELECT CAST(ROW('Bob', 38) AS ROW(name VARCHAR(10), age INTEGER));
  ```

## Working with timestamp data<a name="data-types-timestamps"></a>

This section describes some considerations for working with timestamp data in Athena\.

### Format for writing timestamp data to Amazon S3 objects<a name="data-types-timestamps-writing-to-s3-objects"></a>

The format in which timestamp data should be written into Amazon S3 objects depends on both the column data type and the [SerDe library](https://docs.aws.amazon.com/athena/latest/ug/supported-serdes.html) that you use\.
+ If you have a table column of type `DATE`, Athena expects the corresponding column or property of the data to be a string in the ISO format `YYYY-MM-DD`, or a built\-in date type like those for Parquet or ORC\.
+ If you have a table column of type `TIME`, Athena expects the corresponding column or property of the data to be a string in the ISO format `HH:MM:DD`, or a built\-in time type like those for Parquet or ORC\.
+ If you have a table column of type `TIMESTAMP`, Athena expects the corresponding column or property of the data to be a string in the format `YYYY-MM-DD HH:MM:SS.SSS` \(note the space between the date and time\), or a built\-in time type like those for Parquet, ORC, or Ion\.
**Note**  
OpenCSVSerDe timestamps are an exception and must be encoded as millisecond resolution UNIX epochs\.

### Ensuring that time\-partitioned data matches the timestamp field in a record<a name="data-types-timestamps-time-partitioned-data-and-timestamp-fields"></a>

The producer of the data must make sure partition values align with the data within the partition\. For example, if your data has a `timestamp` property and you use Kinesis Data Firehose to load the data into Amazon S3, you must use [dynamic partitioning](https://docs.aws.amazon.com/firehose/latest/dev/dynamic-partitioning.html) because the default partitioning of Kinesis Data Firehose is wall\-clock\-based\.

### Use string as the data type for partition keys<a name="data-types-timestamps-partition-key-types"></a>

For performance reasons, it is preferable to use `STRING` as the data type for partition keys\. Even though Athena recognizes partition values in the format `YYYY-MM-DD` as dates when you use the `DATE` type, this can lead to poor performance\. For this reason, we recommend that you use the `STRING` data type for partition keys instead\.

### How to write queries for timestamp fields that are also time\-partitioned<a name="data-types-timestamps-how-to-write-queries-for-timestamp-fields-that-are-also-time-partitioned"></a>

How you write queries for timestamp fields that are time\-partitioned depends on the type of table that you want to query\.

#### Hive tables<a name="data-types-timestamps-hive-tables"></a>

With the Hive tables most commonly used in Athena, the query engine has no knowledge of relationships between columns and partition keys\. For this reason, you must always add predicates in your queries for both the column and the partition key\.

For example, suppose you have an `event_time` column and an `event_date` partition key and want to query events between 23:00 and 03:00\. In this case, you must include predicates in your query for both the column and the partition key, as in the following example\.

```
WHERE event_time BETWEEN start_time AND end_time 
  AND event_date BETWEEN start_time_date AND end_time_date
```

#### Iceberg tables<a name="data-types-timestamps-iceberg-tables"></a>

With Iceberg tables, you can use computed partition values, which simplifies your queries\. For example, suppose your Iceberg table was created with a `PARTITIONED BY` clause like the following:

```
PARTITIONED BY (event_date month(event_time))
```

In this case, the query engine automatically prunes partitions based on the values of the `event_time` predicates\. Because of this, your query only needs to specify a predicate for `event_time`, as in the following example\.

```
WHERE event_time BETWEEN start_time AND end_time
```

For more information, see [Creating Iceberg tables](querying-iceberg-creating-tables.md)\.