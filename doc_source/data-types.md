# Data Types<a name="data-types"></a>

When you run `CREATE TABLE`, you must specify column names and their data types\. For a complete syntax of this command, see [CREATE TABLE](create-table.md)\.

## List of Supported Data Types in Athena<a name="list-of-supported-data-types-in-ate"></a>

Athena supports the following data types: 
+ `BOOLEAN`\. Values are `true` and `false`\.
+ Integer types
  + `TINYINT`\. A 8\-bit signed `INTEGER` in two’s complement format, with a minimum value of \-2^7 and a maximum value of 2^7\-1\.
  + `SMALLINT`\. A 16\-bit signed `INTEGER` in two’s complement format, with a minimum value of \-2^15 and a maximum value of 2^15\-1\.
  + `INT`\. Athena combines two different implementations of the `INTEGER` data type\. In Data Definition Language \(DDL\) queries, Athena uses the `INT` data type\. In all other queries, Athena uses the `INTEGER` data type, where `INTEGER` is represented as a 32\-bit signed value in two's complement format, with a minimum value of\-2^31 and a maximum value of 2^31\-1\. In the JDBC driver, `INTEGER` is returned, to ensure compatibility with business analytics applications\.
  + `BIGINT`\.A 64\-bit signed `INTEGER` in two’s complement format, with a minimum value of \-2^63 and a maximum value of 2^63\-1\.
+ Floating\-point types
  + `DOUBLE`
  + `FLOAT`
+ Fixed precision type
  + `DECIMAL [ (precision, scale) ]`, where `precision` is the total number of digits, and `scale` \(optional\) is the number of digits in fractional part, the default is 0\. For example, use these type definitions: `DECIMAL(11,5)`, `DECIMAL(15)`\. 

    To specify decimal values as literals, such as when selecting rows with a specific decimal value in a query DDL expression, specify the `DECIMAL` type definition, and list the decimal value as a literal \(in single quotes\) in your query, as in this example: `decimal_value = DECIMAL '0.12'`\. 
+ String types
  + `CHAR`\. Fixed length character data, with a specified length between 1 and 255, such as `char(10)`\. For more information, see [CHAR Hive Data Type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-char)\.
  + `VARCHAR`\. Variable length character data, with a specified length between 1 and 65535, such as `varchar(10)`\. For more information, see [VARCHAR Hive Data Type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-varchar)\. 
+ `BINARY` \(for data in Parquet\)
+ Date and time types
  + `DATE`, in the UNIX format, such as `YYYY-MM-DD`\.
  + `TIMESTAMP`\. Instant in time and date in the UNiX format, such as `yyyy-mm-dd hh:mm:ss[.f...]`\. For example, `TIMESTAMP '2008-09-15 03:04:05.324'`\. This format uses the session time zone\.
+ Structural types
  + `ARRAY` < data\_type >
  + `MAP` < primitive\_type, data\_type >
  + `STRUCT` < col\_name : data\_type \[COMMENT col\_comment\] \[, \.\.\.\] >

For information about supported data type mappings between types in Athena, the JDBC driver, and Java data types, see the *"Data Types"* section in the [JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf)\. 