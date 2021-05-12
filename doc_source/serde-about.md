# Using a SerDe<a name="serde-about"></a>

A SerDe \(Serializer/Deserializer\) is a way in which Athena interacts with data in various formats\.

It is the SerDe you specify, and not the DDL, that defines the table schema\. In other words, the SerDe can override the DDL configuration that you specify in Athena when you create your table\.

## To Use a SerDe in Queries<a name="to-use-a-serde"></a>

To use a SerDe when creating a table in Athena, use one of the following methods:
+ Specify `ROW FORMAT DELIMITED` and then use DDL statements to specify field delimiters, as in the following example\. When you specify `ROW FORMAT DELIMITED`, Athena uses the LazySimpleSerDe by default\.

  ```
  ROW FORMAT DELIMITED 
  FIELDS TERMINATED BY ','
  ESCAPED BY '\\'
  COLLECTION ITEMS TERMINATED BY '|'
  MAP KEYS TERMINATED BY ':'
  ```
+ Use `ROW FORMAT SERDE` to explicitly specify the type of SerDe that Athena should use when it reads and writes data to the table\. The following example specifies the LazySimpleSerDe\. To specify the delimiters, use `WITH SERDEPROPERTIES`\. The properties specified by `WITH SERDEPROPERTIES` correspond to the separate statements \(like `FIELDS TERMINATED BY`\) in the `ROW FORMAT DELIMITED` example\.

  ```
  ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
  WITH SERDEPROPERTIES (
  'serialization.format' = ',',
  'field.delim' = ',',
  'collection.delim' = '|',
  'mapkey.delim' = ':',
  'escape.delim' = '\\'
  )
  ```