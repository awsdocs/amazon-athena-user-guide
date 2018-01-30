# OpenCSVSerDe for Processing CSV<a name="csv"></a>

When you create a table from a CSV file in Athena, determine what types of values it contains:

+ If the file contains values enclosed in quotes, use the [OpenCSV SerDe](https://cwiki.apache.org/confluence/display/Hive/CSV+Serde) to deserialize the values in Athena\.

+ If the file does not contain values enclosed in quotes, you can omit specifying any SerDe\. In this case, Athena uses the default `LazySimpleSerDe`\. For information, see LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files\.

## CSV SerDe \(OpenCSVSerde\)<a name="csv-serde-opencsvserde"></a>

The [OpenCSV SerDe](https://cwiki.apache.org/confluence/display/Hive/CSV+Serde) behaves as follows:

+ Allows you to specify separator, quote, and escape characters, such as: `WITH SERDEPROPERTIES ("separatorChar" = ",", "quoteChar" = "`", "escapeChar" = "\\" )` 

+ Does not support embedded line breaks in CSV files\.

+ Converts all column type values to `STRING`\.

+ To recognize data types other than `STRING`, relies on the Presto parser and converts the values from `STRING` into those data types if it can recognize them\.

In particular, for data types other than `STRING` this SerDe behaves as follows:

+ Recognizes `BOOLEAN`, `BIGINT`, `INT`, and `DOUBLE` data types and parses them without changes\.

+ Recognizes the `TIMESTAMP` type if it is specified in the UNIX format, such as `yyyy-mm-dd hh:mm:ss[.f...]`, as the type `LONG`\.

+ Does not support `TIMESTAMP` in the JDBC\-compliant `java.sql.Timestamp` format, such as `"YYYY-MM-DD HH:MM:SS.fffffffff"` \(9 decimal place precision\)\. If you are processing CSV files from Hive, use the UNIX format for `TIMESTAMP`\.

+ Recognizes the `DATE` type if it is specified in the UNIX format, such as `YYYY-MM-DD`, as the type `LONG`\.

+ Does not support `DATE` in another format\. If you are processing CSV files from Hive, use the UNIX format for `DATE`\.

### SerDe Name<a name="serde-name"></a>

 [CSV SerDe](https://cwiki.apache.org/confluence/display/Hive/CSV+Serde) 

### Library Name<a name="library-name"></a>

To use this SerDe, specify its fully qualified class name in `ROW FORMAT`, also specify the delimiters inside `SERDEPROPERTIES`, as follows:

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

This example presumes a source CSV file saved in `s3://mybucket/mycsv/` with the following data contents:

```
"a1","a2","a3","a4"
"1","2","abc","def"
"a","a1","abc3","ab4"
```

Use a `CREATE TABLE` statement to create an Athena table based on this CSV file and reference the OpenCSVSerDe class in `ROW FORMAT`, also specifying SerDe properties for character separator, quote character, and escape character\.

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
   'quoteChar' = '\"',
   'escapeChar' = '\\'
   )
STORED AS TEXTFILE
LOCATION 's3://location/of/csv/';
```

Query all values in the table\.

```
SELECT * FROM myopencsvtable;
```

The query returns the following values\.

```
col1     col2    col3    col4
-----------------------------
a1       a2      a3      a4
1        2       abc     def
a        a1      abc3    ab4
```

**Note**  
The flight table data comes from [Flights](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time) provided by US Department of Transportation, [Bureau of Transportation Statistics](http://www.transtats.bts.gov/)\. Desaturated from original\.