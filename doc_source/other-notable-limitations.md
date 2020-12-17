# Considerations and Limitations for SQL Queries in Amazon Athena<a name="other-notable-limitations"></a>

When running queries in Athena, keep in mind the following considerations and limitations:
+ **Stored procedures** – Stored procedures are not supported\.
+ **Parameterized queries** – Parameterized queries are not supported\. However, you can create user\-defined functions that you can call in the body of a query\. For more information , see [Querying with User Defined Functions \(Preview\)](querying-udf.md)\.
+ **Maximum number of partitions** – The maximum number of partitions you can create with `CREATE TABLE AS SELECT` \(CTAS\) statements is 100\. For information, see [CREATE TABLE AS](create-table-as.md)\. For a workaround, see [Using CTAS and INSERT INTO to Create a Table with More Than 100 Partitions](ctas-insert-into.md)\.
+ **Unsupported statements** – The following statements are not supported:
  + `PREPARED` statements are not supported\. You cannot run `EXECUTE` with `USING`\.
  + `CREATE TABLE LIKE` is not supported\.
  + `DESCRIBE INPUT` and `DESCRIBE OUTPUT` is not supported\.
  + `EXPLAIN` statements are not supported\.
+ **Presto federated connectors** – [Presto federated connectors](https://prestodb.io/docs/0.172/connector.html) are not supported\. Use Amazon Athena Federated Query to connect data sources\. For more information, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md)\.
+ **Querying Parquet columns with complex data types ** – When you query columns with complex data types \(`array`, `map`, `struct`\), and are using Parquet for storing data, Athena currently reads an entire row of data instead of selectively reading only the specified columns\. This is a known issue\.
+ **Timeouts on tables with many partitions** – Athena may time out when querying a table that has many thousands of partitions\. This can happen when the table has many partitions that are not of type `string`\. When you use type `string`, Athena prunes partitions at the metastore level\. However, when you use other data types, Athena prunes partitions on the server side\. The more partitions you have, the longer this process takes and the more likely your queries are to time out\. To resolve this issue, set your partition type to `string` so that Athena prunes partitions at the metastore level\. This reduces overhead and prevents queries from timing out\.
+ **Amazon S3 Glacier storage** – Athena does not support querying the data in the [S3 Glacier](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html#sc-glacier) or S3 Glacier Deep Archive storage classes\. Objects in the S3 Glacier storage class are ignored\. Objects in the S3 Glacier Deep Archive storage class that are queried result in the error message The operation is not valid for the object's storage class\. Data that is moved or transitioned to one of these classes are no longer readable or queryable by Athena even after storage class objects are restored\. To make the restored objects that you want to query readable by Athena, copy the restored objects back into Amazon S3 to change their storage class\.
+ **Amazon S3 access points** – You cannot use an Amazon S3 access point in a `LOCATION` clause\. However, as long the as the Amazon S3 bucket policy does not explicitly deny requests to objects not made through Amazon S3 access points, the objects should be accessible from Athena for requestors that have the right object access permissions\.
+ **Files treated as hidden** – Athena treats source files that start with an underscore \(`_`\) or a dot \(`.`\) as hidden\. To work around this limitation, rename the files\.

## Cross\-Regional Queries<a name="cross-region-limitations"></a>

Athena supports queries across only the following Regions\. Queries across other Regions may produce the error message InvalidToken: The provided token is malformed or otherwise invalid\.


| Region Name | Region Code | 
| --- | --- | 
| Asia Pacific \(Tokyo\) | ap\-northeast\-1 | 
| Asia Pacific \(Seoul\) | ap\-northeast\-2 | 
| Asia Pacific \(Mumbai\) | ap\-south\-1 | 
| Asia Pacific \(Singapore\) | ap\-southeast\-1 | 
| Asia Pacific \(Sydney\) | ap\-southeast\-2 | 
| Canada \(Central\) | ca\-central\-1 | 
| Europe \(Frankfurt\) | eu\-central\-1 | 
| Europe \(Stockholm\) | eu\-north\-1 | 
| Europe \(Ireland\) | eu\-west\-1 | 
| Europe \(London\) | eu\-west\-2 | 
| Europe \(Paris\) | eu\-west\-3 | 
| South America \(São Paulo\) | sa\-east\-1 | 
| US East \(N\. Virginia\) | us\-east\-1 | 
| US East \(Ohio\) | us\-east\-2 | 
| US West \(N\. California\) | us\-west\-1 | 
| US West \(Oregon\) | us\-west\-2 | 