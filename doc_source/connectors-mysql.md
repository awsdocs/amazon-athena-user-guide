# Amazon Athena MySQL connector<a name="connectors-mysql"></a>

The Amazon Athena Lambda MySQL connector enables Amazon Athena to access MySQL databases\.

## Prerequisites<a name="connectors-mysql-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a data source connector](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-mysql-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.
+ Because Athena converts queries to lower case, MySQL table names must be in lower case\. For example, Athena queries against a table named `myTable` will fail\.

## Terms<a name="connectors-mysql-terms"></a>

The following terms relate to the MySQL connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-mysql-parameters"></a>

Use the Lambda environment variables in this section to configure the MySQL connector\.

### Connection string<a name="connectors-mysql-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
mysql://${jdbc_connection_string}
```

**Note**  
If you receive the error java\.sql\.SQLException: Zero date value prohibited when doing a `SELECT` query on a MySQL table, add the following parameter to your connection string:  

```
zeroDateTimeBehavior=convertToNull
```
For more information, see [Error 'Zero date value prohibited' while trying to select from MySQL table](https://github.com/awslabs/aws-athena-query-federation/issues/760) on GitHub\.com\.

### Using a multiplexing handler<a name="connectors-mysql-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | MySqlMuxCompositeHandler | 
| Metadata handler | MySqlMuxMetadataHandler | 
| Record handler | MySqlMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-mysql-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the environment variable with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is mymysqlcatalog, then the environment variable name is mymysqlcatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a MySql MUX Lambda function that supports two database instances: `mysql1` \(the default\), and `mysql2`\.


****  

| Property | Value | 
| --- | --- | 
| default | mysql://jdbc:mysql://mysql2\.host:3333/default?user=sample2&password=sample2 | 
| mysql\_catalog1\_connection\_string | mysql://jdbc:mysql://mysql1\.host:3306/default?$\{Test/RDS/MySql1\} | 
| mysql\_catalog2\_connection\_string | mysql://jdbc:mysql://mysql2\.host:3333/default?user=sample2&password=sample2 | 

#### Providing credentials<a name="connectors-mysql-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${Test/RDS/MySql1}`\.

```
mysql://jdbc:mysql://mysql1.host:3306/default?...&${Test/RDS/MySql1}&...
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
mysql://jdbc:mysql://mysql1host:3306/default?...&user=sample2&password=sample2&...
```

Currently, the MySQL connector recognizes the `user` and `password` JDBC properties\.

### Using a single connection handler<a name="connectors-mysql-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single MySQL instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | MySqlCompositeHandler | 
| Metadata handler | MySqlMetadataHandler | 
| Record handler | MySqlRecordHandler | 

#### Single connection handler parameters<a name="connectors-mysql-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single MySQL instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | mysql://mysql1\.host:3306/default?secret=Test/RDS/MySql1 | 

### Spill parameters<a name="connectors-mysql-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-mysql-data-type-support"></a>

The following table shows the corresponding data types for JDBC and Arrow\.


****  

| JDBC | Arrow | 
| --- | --- | 
| Boolean | Bit | 
| Integer | Tiny | 
| Short | Smallint | 
| Integer | Int | 
| Long | Bigint | 
| float | Float4 | 
| Double | Float8 | 
| Date | DateDay | 
| Timestamp | DateMilli | 
| String | Varchar | 
| Bytes | Varbinary | 
| BigDecimal | Decimal | 
| ARRAY | List | 

## Partitions and splits<a name="connectors-mysql-partitions-and-splits"></a>

Partitions are used to determine how to generate splits for the connector\. Athena constructs a synthetic column of type `varchar` that represents the partitioning scheme for the table to help the connector generate splits\. The connector does not modify the actual table definition\.

## Performance tuning<a name="connectors-mysql-performance-tuning"></a>

MySQL supports native partitions\. The Athena Lambda connector can retrieve data from these partitions in parallel\. If you want to query very large datasets with uniform partition distribution, native partitioning is highly recommended\.

The Lambda function performs predicate pushdown to decrease the data scanned by the query\. However, selecting a subset of columns sometimes results in a longer query execution runtime\. `LIMIT` clauses reduce the amount of data scanned, but if you do not provide a predicate, you should expect `SELECT` queries with a `LIMIT` clause to scan at least 16 MB of data\.

## License information<a name="connectors-mysql-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-mysql/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-mysql/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-mysql-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-mysql/pom.xml) file for the MySQL connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-mysql) on GitHub\.com\.