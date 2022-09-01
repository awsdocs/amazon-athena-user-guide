# Amazon Athena Snowflake connector<a name="connectors-snowflake"></a>

The Amazon Athena connector for [Snowflake](https://www.snowflake.com/) enables Amazon Athena to run SQL queries on data stored in your Snowflake SQL database or RDS instances using JDBC\.

## Prerequisites<a name="connectors-snowflake-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-snowflake-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.
+ Currently, Snowflake views are not supported\.
+ In Snowflake, because object names are case sensitive, two tables can have the same name in lower and upper case \(for example, `EMPLOYEE` and `employee`\)\. In Athena Federated Query, schema table names are provided to the Lambda function in lower case\. To work around this issue, you can provide `@schemaCase` query hints to retrieve the data from the tables that have case sensitive names\. Following are two sample queries with query hints\.

  ```
  SELECT * 
  FROM "lambda:snowflakeconnector".SYSTEM."MY_TABLE@schemaCase=upper&tableCase=upper"
  ```

  ```
  SELECT * 
  FROM "lambda:snowflakeconnector".SYSTEM."MY_TABLE@schemaCase=upper&tableCase=lower"
  ```

## Terms<a name="connectors-snowflake-terms"></a>

The following terms relate to the Snowflake connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-snowflake-parameters"></a>

Use the Lambda environment variables in this section to configure the Snowflake connector\.

### Connection string<a name="connectors-snowflake-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
snowflake://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-snowflake-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | SnowflakeMuxCompositeHandler | 
| Metadata handler | SnowflakeMuxMetadataHandler | 
| Record handler | SnowflakeMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-snowflake-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the string with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is mysnowflakecatalog, then the environment variable name is mysnowflakecatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a Snowflake MUX Lambda function that supports two database instances: `snowflake1` \(the default\), and `snowflake2`\.


****  

| Property | Value | 
| --- | --- | 
| default | snowflake://jdbc:snowflake://snowflake1\.host:port/?warehouse=warehousename&db=db1&schema=schema1&$\{Test/RDS/Snowflake1\} | 
| snowflake\_catalog1\_connection\_string | snowflake://jdbc:snowflake://snowflake1\.host:port/?warehouse=warehousename&db=db1&schema=schema1$\{Test/RDS/Snowflake1\} | 
| snowflake\_catalog2\_connection\_string | snowflake://jdbc:snowflake://snowflake2\.host:port/?warehouse=warehousename&db=db1&schema=schema1&user=sample2&password=sample2 | 

#### Providing credentials<a name="connectors-snowflake-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${Test/RDS/Snowflake1}`\.

```
snowflake://jdbc:snowflake://snowflake1.host:port/?warehouse=warehousename&db=db1&schema=schema1${Test/RDS/Snowflake1}&... 
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
snowflake://jdbc:snowflake://snowflake1.host:port/warehouse=warehousename&db=db1&schema=schema1&user=sample2&password=sample2&... 
```

Currently, Snowflake recognizes the `user` and `password` JDBC properties\. It also accepts the user name and password in the format *username*`/`*password* without the keys `user` or `password`\.

### Using a single connection handler<a name="connectors-snowflake-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single Snowflake instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | SnowflakeCompositeHandler | 
| Metadata handler | SnowflakeMetadataHandler | 
| Record handler | SnowflakeRecordHandler | 

#### Single connection handler parameters<a name="connectors-snowflake-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. This string is used when a catalog is not recognized\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single Snowflake instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | snowflake://jdbc:snowflake://snowflake1\.host:port/?secret=Test/RDS/Snowflake1 | 

### Spill parameters<a name="connectors-snowflake-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

### PageCount parameter<a name="connectors-snowflake-pagecount-parameter"></a>

Limits the number of records for each partition\. The default value is 500000

### PartitionLimit parameter<a name="connectors-snowflake-partitionlimit-parameter"></a>

Limits the number of partitions\. The default value is 10\. A large number may cause a timeout issue\. If you encounter a timeout error, set this parameter to a lower value\.

## Data type support<a name="connectors-snowflake-data-type-support"></a>

The following table shows the corresponding data types for JDBC and Apache Arrow\.


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

## Data type conversions<a name="connectors-snowflake-data-type-conversions"></a>

In addition to the JDBC to Arrow conversions, the connector performs certain other conversions to make the Snowflake source and Athena data types compatible\. These conversions help ensure that queries get executed successfully\. The following table shows these conversions\.


****  

| Source data type \(Snowflake\) | Converted data type \(Athena\) | 
| --- | --- | 
| TIMESTAMP | TIMESTAMPMILLI | 
| DATE | TIMESTAMPMILLI | 
| INTEGER | INT | 
| DECIMAL | BIGINT | 
| TIMESTAMP\_NTZ | TIMESTAMPMILLI | 

All other unsupported data types are converted to `VARCHAR`\.

## Partitions and splits<a name="connectors-snowflake-partitions-and-splits"></a>

Partitions are used to determine how to generate splits for the connector\. Athena constructs a synthetic column of type `varchar` that represents the partitioning scheme for the table to help the connector generate splits\. The connector does not modify the actual table definition\.

## Performance tuning<a name="connectors-snowflake-performance-tuning"></a>

For optimal performance, use filters in queries whenever possible\. In addition, we highly recommend native partitioning to retrieve huge datasets that have uniform partition distribution\.

## License information<a name="connectors-snowflake-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-snowflake/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-snowflake/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-snowflake-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-snowflake/pom.xml) file for the Snowflake connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-snowflake) on GitHub\.com\.