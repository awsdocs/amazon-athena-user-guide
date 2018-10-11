# Limitations<a name="other-notable-limitations"></a>

Athena does not support the following features, which are supported by an open source Presto version 0\.172\.
+ User\-defined functions \(UDFs or UDAFs\)\.
+ Stored procedures\.
+ A particular subset of data types is supported\. For more information, see [Data Types](data-types.md)\.
+ `INSERT INTO` statements\. 
+ The maximum number of partitions you can create in the `CREATE TABLE AS SELECT` \(CTAS\) statements is 100\. For information, see [CREATE TABLE AS](create-table-as.md)\.
+ `PREPARED` statements\. You cannot run `EXECUTE` with `USING`\.
+ `CREATE TABLE LIKE`\.
+ `DESCRIBE INPUT` and `DESCRIBE OUTPUT`\.
+ `EXPLAIN` statements\.
+ Federated connectors\. For more information, see [Connectors](https://prestodb.io/docs/0.172/connector.html)\.
+ When you query columns with complex data types \(`array`, `map`, `struct`\), and are using Parquet for storing data, Athena currently reads an entire row of data, instead of selectively reading only the specified columns as expected\. This is a known issue\.