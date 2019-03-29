# OpenCSVSerDe for Processing CSV<a name="csv"></a>

When you create a table from CSV data in Athena, determine what types of values it contains:
+ If data contains values enclosed in double quotes \(`"`\), you can use the [OpenCSV SerDe](https://cwiki.apache.org/confluence/display/Hive/CSV+Serde) to deserialize the values in Athena\. In the following sections, note the behavior of this SerDe with `STRING` data types\.
+ If data does not contain values enclosed in double quotes \(`"`\), you can omit specifying any SerDe\. In this case, Athena uses the default `LazySimpleSerDe`\. For information, see [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md)\.

## CSV SerDe \(OpenCSVSerDe\)<a name="csv-serde-opencsvserde"></a>

The [OpenCSV SerDe](https://cwiki.apache.org/confluence/display/Hive/CSV+Serde) behaves as follows:
+ Converts all column type values to `STRING`\.
+ To recognize data types other than `STRING`, relies on the Presto parser and converts the values from `STRING` into those data types if it can recognize them\.
+ Uses double quotes \(`"`\) as the default quote character, and allows you to specify separator, quote, and escape characters, such as: 

  ```
  WITH SERDEPROPERTIES ("separatorChar" = ",", "quoteChar" = "`", "escapeChar" = "\\" )
  ```
+ Cannot escape `\t` or `\n` directly\. To escape them, use `"escapeChar" = "\\"`\. See the example in this topic\.
+ Does not support embedded line breaks in CSV files\.

**Note**  
When you use Athena with OpenCSVSerDe, the SerDe converts all column types to `STRING`\. Next, the parser in Athena parses the values from `STRING` into actual types based on what it finds\. For example, it parses the values into `BOOLEAN`, `BIGINT`, `INT`, and `DOUBLE` data types when it can discern them\. If the values are in `TIMESTAMP` in the UNIX format, Athena parses them as `TIMESTAMP`\. If the values are in `TIMESTAMP` in Hive format, Athena parses them as `INT`\. `DATE` type values are also parsed as `INT`\.   
 To further convert columns to the desired type in a table, you can [create a view](views.md) over the table and use `CAST` to convert to the desired type\.

For data types *other* than `STRING`, when the parser in Athena can recognize them, this SerDe behaves as follows:
+ Recognizes `BOOLEAN`, `BIGINT`, `INT`, and `DOUBLE` data types and parses them without changes\.
+ Recognizes the `TIMESTAMP` type if it is specified in the UNIX format, such as `yyyy-mm-dd hh:mm:ss[.f...]`, as the type `LONG`\.
+ Does not support `TIMESTAMP` in the JDBC\-compliant `java.sql.Timestamp` format, such as `"YYYY-MM-DD HH:MM:SS.fffffffff"` \(9 decimal place precision\)\. If you are processing CSV data from Hive, use the UNIX format for `TIMESTAMP`\.
+ Recognizes the `DATE` type if it is specified in the UNIX format, such as `YYYY-MM-DD`, as the type `LONG`\.
+ Does not support `DATE` in another format\. If you are processing CSV data from Hive, use the UNIX format for `DATE`\.

**Example Example: Escaping `\t` or `\n`**  
Consider the following test data:  

```
" \\t\\t\\n 123 \\t\\t\\n ",abc
" 456 ",xyz
```
The following statement creates a table in Athena, specifying that `"escapeChar" = "\\"`\.   

```
CREATE EXTERNAL TABLE test1 (
f1 string,
s2 string) 
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde' 
WITH SERDEPROPERTIES ("separatorChar" = ",", "escapeChar" = "\\") 
LOCATION 's3://user-test-region/dataset/test1/'
```
Next, run the following query:   

```
select * from test1;
```
It returns this result, correctly escaping `\t` or `\n`:  

```
f1            s2
\t\t\n 123 \t\t\n            abc
456                          xyz
```

### SerDe Name<a name="serde-name"></a>

 [CSV SerDe](https://cwiki.apache.org/confluence/display/Hive/CSV+Serde) 

### Library Name<a name="library-name"></a>

To use this SerDe, specify its fully qualified class name in `ROW FORMAT`\. Also specify the delimiters inside `SERDEPROPERTIES`, as follows:

```
...
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
  "separatorChar" = ",",
  "quoteChar"     = "`",
  "escapeChar"    = "\\"
)
```

### Example<a name="example"></a>

This example presumes data in CSV saved in `s3://mybucket/mycsv/` with the following contents:

```
"a1","a2","a3","a4"
"1","2","abc","def"
"a","a1","abc3","ab4"
```

Use a `CREATE TABLE` statement to create an Athena table based on the data, and reference the OpenCSVSerDe class in `ROW FORMAT`, also specifying SerDe properties for character separator, quote character, and escape character, as follows:

```
CREATE EXTERNAL TABLE myopencsvtable (
   col1 string,
   col2 string,
   col3 string,
   col4 string
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
   'separatorChar' = ',',
   'quoteChar' = '"',
   'escapeChar' = '\\'
   )
STORED AS TEXTFILE
LOCATION 's3://location/of/csv/';
```

Query all values in the table:

```
SELECT * FROM myopencsvtable;
```

The query returns the following values:

```
col1     col2    col3    col4
-----------------------------
a1       a2      a3      a4
1        2       abc     def
a        a1      abc3    ab4
```

**Note**  
The flight table data comes from [Flights](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time) provided by US Department of Transportation, [Bureau of Transportation Statistics](http://www.transtats.bts.gov/)\. Desaturated from original\.