# Data Types<a name="data-types"></a>

When you run `CREATE TABLE`, you must specify column names and their data types\. For a complete syntax of this command, see [CREATE TABLE](create-table.md)\.

The field `col_name` specifies the name for each column in the table Athena creates, along with the column's data type\. If `col_name` begins with an underscore, enclose it in backticks, for example ``_mycolumn``\.

## List of Supported Data Types in Athena<a name="list-of-supported-data-types-in-ate"></a>

The `data_type` value in the `col_name` field of `CREATE TABLE` can be any of the following:
+   
**primitive\_type**  
  + TINYINT
  + SMALLINT
  + INT\. Athena combines two different implementations of the Integer data type\. In Data Definition Language \(DDL\) queries, Athena uses the INT data type\. In all other queries, Athena uses the INTEGER data type\. In the JDBC driver, INTEGER is returned, to ensure compatibility with the business analytics applications\.
  + BIGINT
  + BOOLEAN
  + DOUBLE
  + FLOAT
  + STRING
  + TIMESTAMP
  + DECIMAL \[ \(precision, scale\) \]
  + DATE \(not supported for PARQUET file\_format\)
  + CHAR\. Fixed length character data, with a specified length between 1 and 255, such as `char(10)`\. For more information, see [CHAR Hive Data Type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-char)\.
  + VARCHAR\. Variable length character data, with a specified length between 1 and 65535, such as `varchar(10)`\. For more information, see [VARCHAR Hive Data Type](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-varchar)\. 
+   
**array\_type**  
  + ARRAY < data\_type >
+   
**map\_type**  
  + MAP < primitive\_type, data\_type >
+   
**struct\_type**  
  + STRUCT < col\_name : data\_type \[COMMENT col\_comment\] \[, \.\.\.\] >

For information about supported data type mappings between types in Athena, the JDBC driver, and Java data types, see the *"Data Types"* section in the [JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.2/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf)\. 