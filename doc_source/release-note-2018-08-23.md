# August 23, 2018<a name="release-note-2018-08-23"></a>

Published on *2018\-08\-23*

Added support for these DDL\-related features and fixed several bugs, as follows: 
+ Added support for `BINARY` and `DATE` data types for data in Parquet, and for `DATE` and `TIMESTAMP` data types for data in Avro\. 
+ Added support for `INT` and `DOUBLE` in DDL queries\. `INTEGER` is an alias to `INT`, and `DOUBLE PRECISION` is an alias to `DOUBLE`\. 
+ Improved performance of `DROP TABLE` and `DROP DATABASE` queries\.
+ Removed the creation of `_$folder$` object in Amazon S3 when a data bucket is empty\.
+ Fixed an issue where `ALTER TABLE ADD PARTITION` threw an error when no partition value was provided\. 
+ Fixed an issue where `DROP TABLE` ignored the database name when checking partitions after the qualified name had been specified in the statement\. 

For more about the data types supported in Athena, see [Data Types](data-types.md)\.

For information about supported data type mappings between types in Athena, the JDBC driver, and Java data types, see the *"Data Types"* section in the [JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.5/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf)\. 