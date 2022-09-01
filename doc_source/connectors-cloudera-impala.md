# Amazon Athena Cloudera Impala connector<a name="connectors-cloudera-impala"></a>

The Amazon Athena Cloudera Impala connector enables Athena to run SQL queries on the [Cloudera Impala](https://www.cloudera.com/products/open-source/apache-hadoop/impala.html) Hadoop distribution\. The connector transforms your Athena SQL queries to their equivalent Impala syntax\.

## Prerequisites<a name="connectors-impala-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-cloudera-impala-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.

## Terms<a name="connectors-cloudera-impala-terms"></a>

The following terms relate to the Cloudera Impala connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-cloudera-impala-parameters"></a>

Use the Lambda environment variables in this section to configure the Cloudera Impala connector\.

### Connection string<a name="connectors-cloudera-impala-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
impala://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-cloudera-impala-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | ImpalaMuxCompositeHandler | 
| Metadata handler | ImpalaMuxMetadataHandler | 
| Record handler | ImpalaMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-cloudera-impala-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the string with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is myimpalacatalog, then the environment variable name is myimpalacatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a Impala MUX Lambda function that supports two database instances: `impala1` \(the default\), and `impala2`\.


****  

| Property | Value | 
| --- | --- | 
| default | impala://jdbc:impala2://impala1:10000/default?$\{Test/RDS/impala1\} | 
| impala\_catalog1\_connection\_string | impala://jdbc:impala2://impala1:10000/default?$\{Test/RDS/impala1\} | 
| impala\_catalog2\_connection\_string | impala://jdbc:impala2://impala2:10000/default?UID=sample&PWD=sample | 

#### Providing credentials<a name="connectors-cloudera-impala-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${Test/RDS/Impala1host}`\.

```
impala://jdbc:impala2://Impala1host:10000/default?...&${Test/RDS/Impala1host}&...
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
impala://jdbc:impala2://Impala1host:10000/default?...&UID=sample2&PWD=sample2&...
```

Currently, Cloudera Impala recognizes the `UID` and `PWD` JDBC properties\.

### Using a single connection handler<a name="connectors-cloudera-impala-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single Cloudera Impala instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | ImpalaCompositeHandler | 
| Metadata handler | ImpalaMetadataHandler | 
| Record handler | ImpalaRecordHandler | 

#### Single connection handler parameters<a name="connectors-cloudera-impala-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. This string is used when a catalog is not recognized\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single Cloudera Impala instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | impala://jdbc:impala://Impala1host:10000/default?secret=$\{Test/RDS/impala1host\} | 

### Spill parameters<a name="connectors-cloudera-impala-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-cloudera-impala-data-type-support"></a>

The following table shows the corresponding data types for JDBC, Cloudera Impala, and Arrow\.


****  

| JDBC | Cloudera Impala | Arrow | 
| --- | --- | --- | 
| Boolean | Boolean | Bit | 
| Integer | TINYINT | Tiny | 
| Short | SMALLINT | Smallint | 
| Integer | INT | Int | 
| Long | BIGINT | Bigint | 
| float | float4 | Float4 | 
| Double | float8 | Float8 | 
| Date | date | DateDay | 
| Timestamp | timestamp | DateMilli | 
| String | VARCHAR | Varchar | 
| Bytes | bytes | Varbinary | 
| BigDecimal | Decimal | Decimal | 
| ARRAY | N/A \(see note\) | List | 

**Note**  
Currently, Cloudera Impala does not support the aggregate types `ARRAY`, `MAP`, `STRUCT`, or `UNIONTYPE`\. Columns of aggregate types are treated as `VARCHAR` columns in SQL\.

## Partitions and splits<a name="connectors-cloudera-impala-partitions-and-splits"></a>

Partitions are used to determine how to generate splits for the connector\. Athena constructs a synthetic column of type `varchar` that represents the partitioning scheme for the table to help the connector generate splits\. The connector does not modify the actual table definition\.

## Performance tuning<a name="connectors-cloudera-impala-performance-tuning"></a>

Cloudera Impala supports static partitions\. The Athena Lambda connector can retrieve data from these partitions in parallel\. If you want to query very large datasets with uniform partition distribution, static partitioning is highly recommended\.

## License information<a name="connectors-impala-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-cloudera-impala/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-cloudera-impala/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-impala-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-cloudera-impala/pom.xml) file for the Cloudera Impala connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-cloudera-impala) on GitHub\.com\.