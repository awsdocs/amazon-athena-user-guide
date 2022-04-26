# Using Amazon Ion SerDe Properties<a name="ion-serde-using-ion-serde-properties"></a>

This topic contains information about the SerDe properties for `CREATE TABLE` statements in Athena\. For more information and examples of Amazon Ion SerDe property usage, see [SerDe Properties](https://github.com/amzn/ion-hive-serde/blob/master/docs/serde-properties.md) in the Amazon Ion Hive SerDe documentation on [GitHub](https://github.com/amzn/ion-hive-serde/tree/master/docs)\.

## Specifying Amazon Ion SerDe Properties<a name="ion-serde-specifying-ion-serde-properties"></a>

To specify properties for the Amazon Ion Hive SerDe in your `CREATE TABLE` statement, use the `WITH SERDEPROPERTIES` clause\. Because `WITH SERDEPROPERTIES` is a subfield of the `ROW FORMAT SERDE` clause, you must specify `ROW FORMAT SERDE` and the Amazon Ion Hive SerDe class path first, as the following syntax shows\.

```
...
ROW FORMAT SERDE
 'com.amazon.ionhiveserde.IonHiveSerDe'
WITH SERDEPROPERTIES (
 'property' = 'value',
 'property' = 'value',
...
)
```

Note that although the `ROW FORMAT SERDE` clause is required if you want to use `WITH SERDEPROPERTIES`, you can use either `STORED AS ION` or the longer `INPUTFORMAT` and `OUTPUTFORMAT` syntax to specify the Amazon Ion format\.

## Amazon Ion SerDe Properties<a name="ion-serde-ion-serde-properties"></a>

Following are the Amazon Ion SerDe properties that can be used in `CREATE TABLE` statements in Athena\.

**ion\.encoding**  
Optional  
Default: `BINARY`  
Values: `BINARY`, `TEXT`  
This property declares whether new values added are serialized as [Amazon Ion binary](https://amzn.github.io/ion-docs/docs/binary.html) or Amazon Ion text format\.  
The following SerDe property example specifies Amazon Ion text format\.  

```
'ion.encoding' = 'TEXT'
```

**ion\.fail\_on\_overflow**  
Optional  
Default: `true`  
Values: `true`, `false`  
Amazon Ion allows for arbitrarily large numerical types while Hive does not\. By default, the SerDe fails if the Amazon Ion value does not fit the Hive column, but you can use the `fail_on_overflow` configuration option to let the value overflow instead of failing\.  
This property can be set at either the table or column level\. To specify it at the table level, specify `ion.fail_on_overflow` as in the following example\. This sets the default behavior for all columns\.  

```
'ion.fail_on_overflow' = 'true'
```
To control a specific column, specify the column name between `ion` and `fail_on_overflow`, delimited by periods, as in the following example\.  

```
'ion.<column>.fail_on_overflow' = 'false'
```

**ion\.path\_extractor\.case\_sensitive**  
Optional  
Default: `false`  
Values: `true`, `false`  
Determines whether to treat Amazon Ion field names as case sensitive\. When `false`, the SerDe ignores case parsing Amazon Ion field names\.  
For example, suppose you have a Hive table schema that defines a field `alias` in lower case and an Amazon Ion document with both an `alias` field and an `ALIAS` field, as in the following example\.  

```
-- Hive Table Schema
alias: STRING

-- Amazon Ion Document
{ 'ALIAS': 'value1'} 
{ 'alias': 'value2'}
```
The following example shows SerDe properties and the resulting extracted table when case sensitivity is set to `false`:  

```
-- Serde properties
'ion.alias.path_extractor' = '(alias)'
'ion.path_extractor.case_sensitive' = 'false'

--Extracted Table
| alias    |
|----------|
| "value1" |
| "value2" |
```
The following example shows SerDe properties and the resulting extracted table when case sensitivity is set to `true`:  

```
-- Serde properties
'ion.alias.path_extractor' = '(alias)'
'ion.path_extractor.case_sensitive' = 'true'

--Extracted Table
| alias    |
|----------|
| "value2" |
```
In the second case, `value1` for the `ALIAS` field is ignored when case sensitivity is set to `true` and the path extractor is specified as `alias`\.

**ion\.*<column>*\.path\_extractor**  
Optional  
Default: NA  
Values: String with search path  
Creates a path extractor with the specified search path for the given column\. Path extractors map Amazon Ion fields to Hive columns\. If no path extractors are specified, Athena dynamically creates path extractors at run time based on column names\.  
The following example path extractor maps the `example_ion_field` to the `example_hive_column`\.  

```
'ion.example_hive_column.path_extractor' = '(example_ion_field)'
```
For more information about path extractors and search paths, see [Using Path Extractors](ion-serde-using-path-extractors.md)\.

**ion\.timestamp\.serialization\_offset**  
Optional  
Default: `'Z'`  
Values: `OFFSET`, where `OFFSET `is represented as `<signal>hh:mm`\. Example values: `01:00`, `+01:00`, `-09:30`, `Z` \(UTC, same as 00:00\)  
Unlike Apache Hive [timestamps](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-timestamp), which have no built\-in time zone and are stored as an offset from the UNIX epoch, Amazon Ion timestamps do have an offset\. Use this property to specify the offset when you serialize to Amazon Ion\.  
The following example adds an offset of one hour\.  

```
'ion.timestamp.serialization_offset' = '+01:00'       
```

**ion\.serialize\_null**  
Optional  
Default: `OMIT`  
Values: `OMIT`, `UNTYPED`, `TYPED`  
The Amazon Ion SerDe can be configured to either serialize or omit columns that have null values\. You can choose to write out strongly typed nulls \(`TYPED`\) or untyped nulls \(`UNTYPED`\)\. Strongly typed nulls are determined based on the default Amazon Ion to Hive type mapping\.  
The following example specifies strongly typed nulls\.  

```
'ion.serialize_null'='TYPED'
```

**ion\.ignore\_malformed**  
Optional  
Default: `false`  
Values: `true`, `false`  
When `true`, ignores malformed entries or the whole file if the SerDe is unable to read it\. For more information, see [Ignore Malformed](https://github.com/amzn/ion-hive-serde/blob/master/docs/serde-properties.md#ignore-malformed) in the documentation on GitHub\.

**ion\.*<column>*\.serialize\_as**  
Optional  
Default: Default type for the column\.  
Values: String containing Amazon Ion type  
Determines the Amazon Ion data type in which a value is serialized\. Because Amazon Ion and Hive types do not always have a direct mapping, a few Hive types have multiple valid data types for serialization\. To serialize data as a non\-default data type, use this property\. For more information about type mapping, see the Amazon Ion [Type mapping](https://github.com/amzn/ion-hive-serde/blob/master/docs/type-mapping.md) page on GitHub\.  
By default, binary Hive columns are serialized as Amazon Ion blobs, but they can also be serialized as an [Amazon Ion clob](https://amzn.github.io/ion-docs/docs/stringclob.html#ion-clob) \(character large object\)\. The following example serializes the column `example_hive_binary_column` as a clob\.  

```
'ion.example_hive_binary_column.serialize_as' = 'clob'       
```