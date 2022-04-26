# Amazon Ion Hive SerDe<a name="ion-serde"></a>

You can use the Amazon Ion Hive SerDe to query data stored in [Amazon Ion](https://amzn.github.io/ion-docs/guides/cookbook.html) format\. Amazon Ion is a richly\-typed, self\-describing, open source data format\. The Amazon Ion format is used by services such as [Amazon Quantum Ledger Database](https://docs.aws.amazon.com/qldb/latest/developerguide/what-is.html) \(Amazon QLDB\) and in the open source SQL query language [PartiQL](https://partiql.org/)\.

Amazon Ion has binary and text formats that are interchangeable\. This feature combines the ease of use of text with the efficiency of binary encoding\.

To query Amazon Ion data from Athena, you can use the [Amazon Ion Hive SerDe](https://github.com/amzn/ion-hive-serde), which serializes and deserializes Amazon Ion data\. Deserialization allows you to run queries on the Amazon Ion data or read it for writing out into a different format like Parquet or ORC\. Serialization lets you generate data in the Amazon Ion format by using `CREATE TABLE AS SELECT` \(CTAS\) or `INSERT INTO` queries to copy data from existing tables\.

**Note**  
Because Amazon Ion is a superset of JSON, you can use the Amazon Ion Hive SerDe to query non\-Amazon Ion JSON datasets\. Unlike other [JSON SerDe libraries](https://docs.aws.amazon.com/athena/latest/ug/json-serde.html), the Amazon Ion SerDe does not expect each row of data to be on a single line\. This feature is useful if you want to query JSON datasets that are in "pretty print" format or otherwise break up the fields in a row with newline characters\.

## SerDe Name<a name="ion-serde-serde-name"></a>
+ [com\.amazon\.ionhiveserde\.IonHiveSerDe](https://github.com/amzn/ion-hive-serde)

## Considerations and Limitations<a name="ion-serde-considerations-and-limitations"></a>
+ **Duplicated Fields** – Amazon Ion structs are ordered and support duplicated fields, while Hive's `STRUCT<>` and `MAP<>` do not\. Thus, when you deserialize a duplicated field from an Amazon Ion struct, a single value is chosen non deterministically, and the others are ignored\.
+ **External Symbol Tables Unsupported** – Currently, Athena does not support external symbol tables or the following Amazon Ion Hive SerDe properties:
  + `ion.catalog.class`
  + `ion.catalog.file`
  + `ion.catalog.url`
  + `ion.symbol_table_imports`
+ **File Extensions** – Amazon Ion uses file extensions to determine which compression codec to use for deserializing Amazon Ion files\. As such, compressed files must have the file extension that corresponds to the compression algorithm used\. For example, if ZSTD is used, corresponding files should have the extension `.zst`\.
+ **Homogeneous Data** – Amazon Ion has no restrictions on the data types that can be used for values in particular fields\. For example, two different Amazon Ion documents might have a field with the same name that have different data types\. However, because Hive uses a schema, all values that you extract to a single Hive column must have the same data type\.
+ **Map Key Type Restrictions** – When you serialize data from another format into Amazon Ion, ensure that the map key type is one of `STRING`, `VARCHAR`, or `CHAR`\. Although Hive allows you to use any primitive data type as a map key, [Amazon Ion symbols](https://amzn.github.io/ion-docs/docs/symbols.html) must be a string type\.
+ **Union Type** – Athena does not currently support the Hive [union type](https://cwiki.apache.org/confluence/display/hive/languagemanual+types/#LanguageManualTypes-UnionTypesunionUnionTypes)\.

**Topics**
+ [SerDe Name](#ion-serde-serde-name)
+ [Considerations and Limitations](#ion-serde-considerations-and-limitations)
+ [Using CREATE TABLE to Create Amazon Ion Tables](ion-serde-using-create-table.md)
+ [Using CTAS and INSERT INTO to Create Amazon Ion Tables](ion-serde-using-ctas-and-insert-into-to-create-ion-tables.md)
+ [Using Amazon Ion SerDe Properties](ion-serde-using-ion-serde-properties.md)
+ [Using Path Extractors](ion-serde-using-path-extractors.md)