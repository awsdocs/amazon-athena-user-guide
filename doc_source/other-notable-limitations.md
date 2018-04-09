# Limitations<a name="other-notable-limitations"></a>

Athena does not support the following features, which are supported by an open source Presto version 0\.172\.
+ User\-defined functions \(UDFs or UDAFs\)\.
+ Stored procedures\.
+ A particular subset of data types is supported\. For more information, see [Data Types](data-types.md)\.
+ `CREATE TABLE AS SELECT` statements\.
+ `INSERT INTO` statements\. 
+ Prepared statements\. You cannot run `EXECUTE` with `USING`\.
+ `CREATE TABLE LIKE`\.
+ `DESCRIBE INPUT` and `DESCRIBE OUTPUT`\.
+ `EXPLAIN` statements\.
+ Federated connectors\. For more information, see [Connectors](https://prestodb.io/docs/0.172/connector.html)\.