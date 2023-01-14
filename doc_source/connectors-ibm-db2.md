# Amazon Athena IBM Db2 connector<a name="connectors-ibm-db2"></a>

The Amazon Athena connector for Db2 enables Amazon Athena to run SQL queries on your IBM Db2 databases using JDBC\.

## Prerequisites<a name="connectors-dbtwo-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-ibm-db2-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.
+ Date and timestamp data types in filter conditions must be cast to appropriate data types\.

## Terms<a name="connectors-ibm-db2-terms"></a>

The following terms relate to the Db2 connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-ibm-db2-parameters"></a>

Use the Lambda environment variables in this section to configure the Db2 connector\.

### Connection string<a name="connectors-ibm-db2-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
dbtwo://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-ibm-db2-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | Db2MuxCompositeHandler | 
| Metadata handler | Db2MuxMetadataHandler | 
| Record handler | Db2MuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-ibm-db2-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the environment variable with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is mydbtwocatalog, then the environment variable name is mydbtwocatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a Db2 MUX Lambda function that supports two database instances: `dbtwo1` \(the default\), and `dbtwo2`\.


****  

| Property | Value | 
| --- | --- | 
| default | dbtwo://jdbc:db2://dbtwo1\.hostname:port/database\_name:$\{secret1\_name\} | 
| dbtwo\_catalog1\_connection\_string | dbtwo://jdbc:db2://dbtwo1\.hostname:port/database\_name:$\{secret1\_name\} | 
| dbtwo\_catalog2\_connection\_string | dbtwo://jdbc:db2://dbtwo2\.hostname:port/database\_name:$\{secret2\_name\} | 

#### Providing credentials<a name="connectors-ibm-db2-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${secret_name}`\.

```
dbtwo://jdbc:db2://hostname:port/database_name:${secret_name}
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
dbtwo://jdbc:db2://hostname:port/database_name:user=user_name;password=password;
```

### Using a single connection handler<a name="connectors-ibm-db2-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single Db2 instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | Db2CompositeHandler | 
| Metadata handler | Db2MetadataHandler | 
| Record handler | Db2RecordHandler | 

#### Single connection handler parameters<a name="connectors-ibm-db2-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single Db2 instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | dbtwo://jdbc:db2://hostname:port/database\_name:$\{secret\_name\}  | 

### Spill parameters<a name="connectors-ibm-db2-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-ibm-db2-data-type-support"></a>

The following table shows the corresponding data types for JDBC and Arrow\.


****  

| Db2 | Arrow | 
| --- | --- | 
| CHAR | VARCHAR | 
| VARCHAR | VARCHAR | 
| DATE | DATEDAY | 
| TIME | VARCHAR | 
| TIMESTAMP | DATEMILLI | 
| DATETIME | DATEMILLI | 
| BOOLEAN | BOOL | 
| SMALLINT | SMALLINT | 
| INTEGER | INT | 
| BIGINT | BIGINT | 
| DECIMAL | DECIMAL | 
| REAL | FLOAT8 | 
| DOUBLE | FLOAT8 | 
| DECFLOAT | FLOAT8 | 

## Partitions and splits<a name="connectors-ibm-db2-partitions-and-splits"></a>

A partition is represented by one or more partition columns of type `varchar`\. The Db2 connector creates partitions using the following organization schemes\.
+ Distribute by hash
+ Partition by range
+ Organize by dimensions

The connector retrieves partition details such as the number of partitions and column name from one or more Db2 metadata tables\. Splits are created based upon the number of partitions identified\. 

## Best practices<a name="connectors-ibm-db2-best-practices"></a>

As a best practice, use predicate pushdown when you query Athena, as in the following examples\.

```
SELECT * 
FROM "lambda:LAMBDA_NAME"."SCHEMA_NAME"."TABLE_NAME" 
WHERE integercol = 2147483647
```

```
SELECT * 
FROM "lambda:LAMBDA_NAME"."SCHEMA_NAME"."TABLE_NAME" 
WHERE timestampcol >= TIMESTAMP '2018-03-25 07:30:58.878'
```

## License information<a name="connectors-dbtwo-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-db2/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-db2/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-dbtwo-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-db2/pom.xml) file for the Db2 connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-db2) on GitHub\.com\.