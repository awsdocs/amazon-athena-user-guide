# Amazon Athena PostgreSQL connector<a name="connectors-postgresql"></a>

The Amazon Athena PostgreSQL connector enables Athena to access your PostgreSQL databases\.

## Prerequisites<a name="connectors-postgres-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a data source connector](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.

## Limitations<a name="connectors-postgresql-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.

## Terms<a name="connectors-postgresql-terms"></a>

The following terms relate to the PostgreSQL connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-postgresql-parameters"></a>

Use the Lambda environment variables in this section to configure the PostgreSQL connector\.

### Connection string<a name="connectors-postgresql-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
postgres://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-postgresql-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | PostGreSqlMuxCompositeHandler | 
| Metadata handler | PostGreSqlMuxMetadataHandler | 
| Record handler | PostGreSqlMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-postgresql-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the environment variable with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is mypostgrescatalog, then the environment variable name is mypostgrescatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a PostGreSql MUX Lambda function that supports two database instances: `postgres1` \(the default\), and `postgres2`\.


****  

| Property | Value | 
| --- | --- | 
| default | postgres://jdbc:postgresql://postgres1\.host:5432/default?$\{Test/RDS/PostGres1\} | 
| postgres\_catalog1\_connection\_string | postgres://jdbc:postgresql://postgres1\.host:5432/default?$\{Test/RDS/PostGres1\} | 
| postgres\_catalog2\_connection\_string | postgres://jdbc:postgresql://postgres2\.host:5432/default?user=sample&password=sample | 

#### Providing credentials<a name="connectors-postgresql-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${Test/RDS/PostGres1}`\.

```
postgres://jdbc:postgresql://postgres1.host:3306/default?...&${Test/RDS/PostGres1}&...
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
postgres://jdbc:postgresql://postgres1.host:3306/default?...&user=sample2&password=sample2&...
```

Currently, the PostgreSQL connector recognizes the `user` and `password` JDBC properties\.

#### Enabling SSL<a name="connectors-postgresql-ssl"></a>

To support SSL in your PostgreSQL connection, append the following to your connection string:

```
&sslmode=verify-ca&sslfactory=org.postgresql.ssl.DefaultJavaSSLFactory
```

**Example**  
The following example connection string does not use SSL\.

```
postgres://jdbc:postgresql://example-asdf-aurora-postgres-endpoint:5432/asdf?user=someuser&password=somepassword
```

To enable SSL, modify the string as follows\.

```
postgres://jdbc:postgresql://example-asdf-aurora-postgres-endpoint:5432/asdf?user=someuser&password=somepassword&sslmode=verify-ca&sslfactory=org.postgresql.ssl.DefaultJavaSSLFactory
```

### Using a single connection handler<a name="connectors-postgresql-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single PostgreSQL instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | PostGreSqlCompositeHandler | 
| Metadata handler | PostGreSqlMetadataHandler | 
| Record handler | PostGreSqlRecordHandler | 

#### Single connection handler parameters<a name="connectors-postgresql-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single PostgreSQL instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | postgres://jdbc:postgresql://postgres1\.host:3306/default?secret=$\{Test/RDS/PostgreSQL1\} | 

### Spill parameters<a name="connectors-postgresql-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-postgresql-data-type-support"></a>

The following table shows the corresponding data types for JDBC, PostGreSQL, and Arrow\.


****  

| JDBC | PostGreSQL | Arrow | 
| --- | --- | --- | 
| Boolean | Boolean | Bit | 
| Integer | N/A | Tiny | 
| Short | smallint | Smallint | 
| Integer | integer | Int | 
| Long | bigint | Bigint | 
| float | float4 | Float4 | 
| Double | float8 | Float8 | 
| Date | date | DateDay | 
| Timestamp | timestamp | DateMilli | 
| String | text | Varchar | 
| Bytes | bytes | Varbinary | 
| BigDecimal | numeric\(p,s\) | Decimal | 
| ARRAY | N/A \(see note\) | List | 

**Note**  
The `ARRAY` type is supported for the PostgreSQL connector with the following constraints: Multidimensional arrays \(`<data_type>[][]` or nested arrays\) are not supported\. Columns with unsupported `ARRAY` data\-types are converted to an array of string elements \(`array<varchar>`\)\.

## Partitions and splits<a name="connectors-postgresql-partitions-and-splits"></a>

Partitions are used to determine how to generate splits for the connector\. Athena constructs a synthetic column of type `varchar` that represents the partitioning scheme for the table to help the connector generate splits\. The connector does not modify the actual table definition\.

## Performance tuning<a name="connectors-postgresql-performance-tuning"></a>

PostgreSQL supports native partitions\. The Athena Lambda connector can retrieve data from these partitions in parallel\. If you want to query very large datasets with uniform partition distribution, native partitioning is highly recommended\.

The Lambda function performs predicate pushdown to decrease the data scanned by the query\. However, selecting a subset of columns sometimes results in a longer query execution runtime\. `LIMIT` clauses reduce the amount of data scanned, but if you do not provide a predicate, you should expect `SELECT` queries with a `LIMIT` clause to scan at least 16 MB of data\.

## See also<a name="connectors-postgresql-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-postgresql/pom.xml) file for the PostgreSQL connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-postgresql) on GitHub\.com\.