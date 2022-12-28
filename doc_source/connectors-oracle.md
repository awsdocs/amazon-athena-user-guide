# Amazon Athena Oracle connector<a name="connectors-oracle"></a>

The Amazon Athena connector for Oracle enables Amazon Athena to run SQL queries on data stored in Oracle running on\-premises or on Amazon EC2 or Amazon RDS\. You can also use the connector to query data on [Oracle exadata](https://www.oracle.com/engineered-systems/exadata/)\.

## Prerequisites<a name="connectors-oracle-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-oracle-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.

## Terms<a name="connectors-oracle-terms"></a>

The following terms relate to the Oracle connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-oracle-parameters"></a>

Use the Lambda environment variables in this section to configure the Oracle connector\.

### Connection string<a name="connectors-oracle-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
oracle://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-oracle-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | OracleMuxCompositeHandler | 
| Metadata handler | OracleMuxMetadataHandler | 
| Record handler | OracleMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-oracle-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the string with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is myoraclecatalog, then the environment variable name is myoraclecatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a Oracle MUX Lambda function that supports two database instances: `oracle1` \(the default\), and `oracle2`\.


****  

| Property | Value | 
| --- | --- | 
| default | oracle://jdbc:oracle:thin:$\{Test/RDS/Oracle1\}@//oracle1\.hostname:port/servicename | 
| oracle\_catalog1\_connection\_string | oracle://jdbc:oracle:thin:$\{Test/RDS/Oracle1\}@//oracle1\.hostname:port/servicename | 
| oracle\_catalog2\_connection\_string | oracle://jdbc:oracle:thin:$\{Test/RDS/Oracle2\}@//oracle2\.hostname:port/servicename | 

#### Providing credentials<a name="connectors-oracle-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${Test/RDS/Oracle}`\.

```
oracle://jdbc:oracle:thin:${Test/RDS/Oracle}@//hostname:port/servicename 
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
oracle://jdbc:oracle:thin:username/password@//hostname:port/servicename
```

Currently, the Oracle connector recognizes the `UID` and `PWD` JDBC properties\.

### Using a single connection handler<a name="connectors-oracle-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single Oracle instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | OracleCompositeHandler | 
| Metadata handler | OracleMetadataHandler | 
| Record handler | OracleRecordHandler | 

#### Single connection handler parameters<a name="connectors-oracle-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. This string is used when a catalog is not recognized\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The connector supports SSL based connections to Amazon RDS instances\. Support is limited to the Transport Layer Security \(TLS\) protocol and to authentication of the server by the client\. Mutual authentication it is not supported in Amazon RDS\. The second row in the table below shows the syntax for using SSL\.

The following example property is for a single Oracle instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | oracle://jdbc:oracle:thin:$\{Test/RDS/Oracle\}@//hostname:port/servicename | 
|  | oracle://jdbc:oracle:thin:$\{Test/RDS/Oracle\}@\(DESCRIPTION=\(ADDRESS=\(PROTOCOL=TCPS\) \(HOST=<HOST\_NAME>\)\(PORT=\)\)\(CONNECT\_DATA=\(SID=\)\)\(SECURITY=\(SSL\_SERVER\_CERT\_DN=\)\)\) | 

### Spill parameters<a name="connectors-oracle-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-oracle-data-type-support"></a>

The following table shows the corresponding data types for JDBC, Oracle, and Arrow\.


****  

| JDBC | Oracle | Arrow | 
| --- | --- | --- | 
| Boolean | boolean | Bit | 
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

## Partitions and splits<a name="connectors-oracle-partitions-and-splits"></a>

Partitions are used to determine how to generate splits for the connector\. Athena constructs a synthetic column of type `varchar` that represents the partitioning scheme for the table to help the connector generate splits\. The connector does not modify the actual table definition\.

## Performance tuning<a name="connectors-oracle-performance-tuning"></a>

Oracle supports native partitions\. The Athena Lambda connector can retrieve data from these partitions in parallel\. If you want to query very large datasets with uniform partition distribution, native partitioning is highly recommended\.

The Lambda function performs predicate pushdown to decrease the data scanned by the query\. `LIMIT` clauses reduce the amount of data scanned, but if you do not provide a predicate, you should expect `SELECT` queries with a `LIMIT` clause to scan at least 16 MB of data\. Selecting a subset of columns significantly speeds up query runtime and reduces data scanned\. The Oracle connector is resilient to throttling due to concurrency\. However, query runtimes tend to be long\.

## License information<a name="connectors-oracle-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-oracle/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-oracle/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-oracle-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-oracle/pom.xml) file for the Oracle connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-oracle) on GitHub\.com\.