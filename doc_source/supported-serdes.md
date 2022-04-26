# Supported SerDes and Data Formats<a name="supported-serdes"></a>

Athena supports creating tables and querying data from CSV, TSV, custom\-delimited, and JSON formats; data from Hadoop\-related formats: ORC, Apache Avro and Parquet; logs from Logstash, AWS CloudTrail logs, and Apache WebServer logs\.

**Note**  
The formats listed in this section are used by Athena for reading data\. For information about formats that Athena uses for writing data when it runs CTAS queries, see [Creating a Table from Query Results \(CTAS\)](ctas.md)\.

To create tables and query data in these formats in Athena, specify a serializer\-deserializer class \(SerDe\) so that Athena knows which format is used and how to parse the data\.

This table lists the data formats supported in Athena and their corresponding SerDe libraries\.

A SerDe is a custom library that tells the data catalog used by Athena how to handle the data\. You specify a SerDe type by listing it explicitly in the `ROW FORMAT` part of your `CREATE TABLE` statement in Athena\. In some cases, you can omit the SerDe name because Athena uses some SerDe types by default for certain types of data formats\.


**Supported Data Formats and SerDes**  

| Data Format | Description | SerDe types supported in Athena | 
| --- | --- | --- | 
| Amazon Ion | Amazon Ion is a richly\-typed, self\-describing data format that is a superset of JSON, developed and open\-sourced by Amazon\. | Use the [Amazon Ion Hive SerDe](ion-serde.md)\. | 
|  Apache Avro  |  A format for storing data in Hadoop that uses JSON\-based schemas for record values\.  |  Use the [Avro SerDe](avro-serde.md)\.  | 
|  Apache Parquet  |  A format for columnar storage of data in Hadoop\.  |  Use the [Parquet SerDe](parquet-serde.md) and SNAPPY compression\.  | 
|  Apache WebServer logs  |  A format for storing logs in Apache WebServer\.  |  Use the [Grok SerDe](grok-serde.md) or [Regex SerDe](regex-serde.md)\.  | 
|  CloudTrail logs  |  A format for storing logs in CloudTrail\.  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/supported-serdes.html)  | 
|  CSV \(Comma\-Separated Values\)  |  For data in CSV, each line represents a data record, and each record consists of one or more fields, separated by commas\.  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/supported-serdes.html)  | 
|  Custom\-Delimited  |  For data in this format, each line represents a data record, and records are separated by a custom single\-character delimiter\.  |  Use the [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md) and specify a custom single\-character delimiter\.  | 
|  JSON \(JavaScript Object Notation\)  |  For JSON data, each line represents a data record, and each record consists of attribute–value pairs and arrays, separated by commas\.  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/supported-serdes.html)  | 
|  Logstash logs  |  A format for storing logs in Logstash\.  |  Use the [Grok SerDe](grok-serde.md)\.  | 
|  ORC \(Optimized Row Columnar\)  |  A format for optimized columnar storage of Hive data\.  |  Use the [ORC SerDe](orc-serde.md) and ZLIB compression\.  | 
|  TSV \(Tab\-Separated Values\)  |  For data in TSV, each line represents a data record, and each record consists of one or more fields, separated by tabs\.  |  Use the [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md) and specify the separator character as `FIELDS TERMINATED BY '\t'`\.  | 

**Topics**
+ [Amazon Ion Hive SerDe](ion-serde.md)
+ [Avro SerDe](avro-serde.md)
+ [CloudTrail SerDe](cloudtrail-serde.md)
+ [Grok SerDe](grok-serde.md)
+ [JSON SerDe Libraries](json-serde.md)
+ [LazySimpleSerDe for CSV](lazy-simple-serde.md)
+ [OpenCSVSerDe](csv-serde.md)
+ [ORC SerDe](orc-serde.md)
+ [Parquet SerDe](parquet-serde.md)
+ [Regex SerDe](regex-serde.md)