# July 23, 2018<a name="release-note-2018-07-23"></a>

Published on *2018\-07\-23*

Made the following improvements and additions:
+ Added support for `BINARY` and `DATE` data types when using the Parquet SerDe\. This allows you to run `CREATE TABLE` DDL queries with all data types on data stored in Parquet\. 
+ Added support for `DATE` and `TIMESTAMP` data types when using the Avro SerDe\. This allows you to run `CREATE TABLE` DDL queries with `DATE` and `TIMESTAMP` data types on data stored in AVRO\. 
+ Added support for `INTEGER` data type as an alias to `INT`\. 
+ Added support for `DOUBLE PRECISION` data type as an alias to `DOUBLE`\. 
+ Improved performance for `DROP TABLE` and `DROP DATABASE` queries\.
+ Fixed a bug where `ALTER TABLE ADD PARTITION` throws errors when no arguments are provided\.
+ Fixed a bug in `DROP TABLE` where it ignored the database name when checking partitions when the qualified name was specified in the statement\.

For more information, see [Data Types](data-types.md)\.

Published lists of reserved words for `DDL` and `SELECT` queries\. For information, see [Reserved Keywords](reserved-words.md)\. 