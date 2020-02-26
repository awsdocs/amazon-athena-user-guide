# Considerations and Limitations for SQL Queries in Amazon Athena<a name="other-notable-limitations"></a>
+ Stored procedures are not supported\.
+ The maximum number of partitions you can create with `CREATE TABLE AS SELECT` \(CTAS\) statements is 100\. For information, see [CREATE TABLE AS](create-table-as.md)\. For a workaround, see [Using CTAS and INSERT INTO to Create a Table with More Than 100 Partitions](ctas-insert-into.md)\.
+ `PREPARED` statements are not supported\. You cannot run `EXECUTE` with `USING`\.
+ `CREATE TABLE LIKE` is not supported\.
+ `DESCRIBE INPUT` and `DESCRIBE OUTPUT` is not supported\.
+ `EXPLAIN` statements are not supported\.
+ [Presto federated connectors](https://prestodb.io/docs/0.172/connector.html) are not supported\. Use Amazon Athena Federated Query \(Preview\) to connect data sources\. For more information, see [Using Amazon Athena Federated Query \(Preview\)](connect-to-a-data-source.md)\.
+ When you query columns with complex data types \(`array`, `map`, `struct`\), and are using Parquet for storing data, Athena currently reads an entire row of data instead of selectively reading only the specified columns\. This is a known issue\.