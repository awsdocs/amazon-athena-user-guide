# Using a SerDe<a name="serde-about"></a>

A SerDe \(Serializer/Deserializer\) is a way in which Athena interacts with data in various formats\.

It is the SerDe you specify, and not the DDL, that defines the table schema\. In other words, the SerDe can override the DDL configuration that you specify in Athena when you create your table\.

## To Use a SerDe in Queries<a name="to-use-a-serde"></a>

To use a SerDe when creating a table in Athena, use one of the following methods:

+ Use DDL statements to describe how to read and write data to the table and do not specify a `ROW FORMAT`, as in this example\. This omits listing the actual SerDe type and the native `LazySimpleSerDe` is used by default\.

In general, Athena uses the `LazySimpleSerDe` if you do not specify a `ROW FORMAT`, or if you specify `ROW FORMAT DELIMITED`\.

```
ROW FORMAT
DELIMITED FIELDS TERMINATED BY ','
ESCAPED BY '\\'
COLLECTION ITEMS TERMINATED BY '|'
MAP KEYS TERMINATED BY ':'
```

+ Explicitly specify the type of SerDe Athena should use when it reads and writes data to the table\. Also, specify additional properties in `SERDEPROPERTIES`, as in this example\.

```
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
WITH SERDEPROPERTIES (
'serialization.format' = ',',
'field.delim' = ',',
'colelction.delim' = '|',
'mapkey.delim' = ':',
'escape.delim' = '\\'
)
```