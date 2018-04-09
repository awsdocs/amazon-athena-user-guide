# Supported SerDes and Data Formats<a name="supported-format"></a>

Athena supports creating tables and querying data from files in CSV, TSV, custom\-delimited, and JSON formats; files from Hadoop\-related formats: ORC, Apache Avro and Parquet; log files from Logstash, AWS CloudTrail logs, and Apache WebServer logs\.

To create tables and query data from files in these formats in Athena, specify a serializer\-deserializer class \(SerDe\) so that Athena knows which format is used and how to parse the data\.

This table lists the data formats supported in Athena and their corresponding SerDe libraries\.

A SerDe is a custom library that tells the data catalog used by Athena how to handle the data\. You specify a SerDe type by listing it explicitly in the `ROW FORMAT` part of your `CREATE TABLE` statement in Athena\. In some cases, you can omit the SerDe name because Athena uses some SerDe types by default for certain types of file formats\.


**Supported Data Formats and SerDes**  

| Data Format | Description | SerDe types supported in Athena | 
| --- | --- | --- | 
|  CSV \(Comma\-Separated Values\)  |  In a CSV file, each line represents a data record, and each record consists of one or more fields, separated by commas\.  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/supported-format.html)  | 
|  TSV \(Tab\-Separated Values\)  |  In a TSV file, each line represents a data record, and each record consists of one or more fields, separated by tabs\.  |  Use the [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md) and specify the separator character as `FIELDS TERMINATED BY '\t'`\.  | 
|  Custom\-Delimited files  |  In a file in this format, each line represents a data record, and records are separated by custom delimiters\.  |  Use the [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md) and specify custom delimiters\.  | 
|  JSON \(JavaScript Object Notation\)  |  In a JSON file, each line represents a data record, and each record consists of attribute–value pairs and arrays, separated by commas\.  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/supported-format.html)  | 
|  Apache Avro  |  A format for storing data in Hadoop that uses JSON\-based schemas for record values\.  |  Use the [Avro SerDe](avro.md)\.  | 
|  ORC \(Optimized Row Columnar\)  |  A format for optimized columnar storage of Hive data\.  |  Use the [ORC SerDe](orc.md) and ZLIB compression\.  | 
|  Apache Parquet  |  A format for columnar storage of data in Hadoop\.  |  Use the [Parquet SerDe](parquet.md) and SNAPPY compression\.  | 
|  Logstash log files  |  A format for storing log files in Logstash\.  |  Use the [Grok SerDe](grok.md)\.  | 
|  Apache WebServer log files  |  A format for storing log files in Apache WebServer\.  |  Use the [RegexSerDe for Processing Apache Web Server Logs](apache.md)\.  | 
|  CloudTrail log files  |  A format for storing log files in CloudTrail\.  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/supported-format.html)  | 

**Topics**
+ [Avro SerDe](avro.md)
+ [RegexSerDe for Processing Apache Web Server Logs](apache.md)
+ [CloudTrail SerDe](cloudtrail.md)
+ [OpenCSVSerDe for Processing CSV](csv.md)
+ [Grok SerDe](grok.md)
+ [JSON SerDe Libraries](json.md)
+ [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md)
+ [ORC SerDe](orc.md)
+ [Parquet SerDe](parquet.md)