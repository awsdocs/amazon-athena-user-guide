# JSON SerDe Libraries<a name="json-serde"></a>

In Athena, you can use SerDe libraries to deserialize JSON data\. Deserialization converts the JSON data so that it can be serialized \(written out\) into a different format like Parquet or ORC\.
+ The native [Hive JSON SerDe](hive-json-serde.md)
+ The [OpenX JSON SerDe](openx-json-serde.md) 
+ The [Amazon Ion Hive SerDe](ion-serde.md)

**Note**  
The Hive and OpenX libraries expect JSON data to be on a single line \(not formatted\), with records separated by a new line character\. The Amazon Ion Hive SerDe does not have that requirement and can be used as an alternative because the Ion data format is a superset of JSON\.

## Library Names<a name="library-names"></a>

Use one of the following:

 [org\.apache\.hive\.hcatalog\.data\.JsonSerDe](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-JSON) 

 [org\.openx\.data\.jsonserde\.JsonSerDe](https://github.com/rcongiu/Hive-JSON-Serde) 

[com\.amazon\.ionhiveserde\.IonHiveSerDe](https://github.com/amzn/ion-hive-serde)

## Additional Resources<a name="json-serdes-additional-resources"></a>

For more information about working with JSON and nested JSON in Athena, see the following resources:
+ [Create Tables in Amazon Athena from Nested JSON and Mappings Using JSONSerDe](http://aws.amazon.com/blogs/big-data/create-tables-in-amazon-athena-from-nested-json-and-mappings-using-jsonserde/) \(AWS Big Data Blog\)
+ [I get errors when I try to read JSON data in Amazon Athena](http://aws.amazon.com/premiumsupport/knowledge-center/error-json-athena/) \(AWS Knowledge Center article\)
+ [hive\-json\-schema](https://github.com/quux00/hive-json-schema) \(GitHub\) – Tool written in Java that generates `CREATE TABLE` statements from example JSON documents\. The `CREATE TABLE` statements that are generated use the OpenX JSON Serde\.