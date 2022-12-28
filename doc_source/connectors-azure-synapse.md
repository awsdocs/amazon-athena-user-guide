# Amazon Athena Azure Synapse connector<a name="connectors-azure-synapse"></a>

The Amazon Athena connector for [Azure Synapse analytics](https://docs.microsoft.com/en-us/azure/synapse-analytics/overview-what-is) enables Amazon Athena to run SQL queries on your Azure Synapse databases using JDBC\.

## Prerequisites<a name="connectors-synapse-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-azure-synapse-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.
+ In filter conditions, you must cast the `Date` and `Timestamp` data types to the appropriate data type\.
+ To search for negative values of type `Real` and `Float`, use the `<=` or `>=` operator\.
+ The `binary`, `varbinary`, `image`, and `rowversion` data types are not supported\.

## Terms<a name="connectors-azure-synapse-terms"></a>

The following terms relate to the Synapse connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-azure-synapse-parameters"></a>

Use the Lambda environment variables in this section to configure the Synapse connector\.

### Connection string<a name="connectors-azure-synapse-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
synapse://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-azure-synapse-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | SynapseMuxCompositeHandler | 
| Metadata handler | SynapseMuxMetadataHandler | 
| Record handler | SynapseMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-azure-synapse-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the string with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is mysynapsecatalog, then the environment variable name is mysynapsecatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a Synapse MUX Lambda function that supports two database instances: `synapse1` \(the default\), and `synapse2`\.


****  

| Property | Value | 
| --- | --- | 
| default | synapse://jdbc:synapse://synapse1\.hostname:port;databaseName=<database\_name>;$\{secret1\_name\} | 
| synapse\_catalog1\_connection\_string | synapse://jdbc:synapse://synapse1\.hostname:port;databaseName=<database\_name>;$\{secret1\_name\} | 
| synapse\_catalog2\_connection\_string | synapse://jdbc:synapse://synapse2\.hostname:port;databaseName=<database\_name>;$\{secret2\_name\} | 

#### Providing credentials<a name="connectors-azure-synapse-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name $\{secret\_name\}\.

```
synapse://jdbc:synapse://hostname:port;databaseName=<database_name>;${secret_name}
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
synapse://jdbc:synapse://hostname:port;databaseName=<database_name>;user=<user>;password=<password>
```

### Using a single connection handler<a name="connectors-azure-synapse-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single Synapse instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | SynapseCompositeHandler | 
| Metadata handler | SynapseMetadataHandler | 
| Record handler | SynapseRecordHandler | 

#### Single connection handler parameters<a name="connectors-azure-synapse-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. This string is used when a catalog is not recognized\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single Synapse instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | synapse://jdbc:sqlserver://hostname:port;databaseName=<database\_name>;$\{secret\_name\} | 

### Spill parameters<a name="connectors-azure-synapse-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-azure-synapse-data-type-support"></a>

The following table shows the corresponding data types for Synapse and Apache Arrow\.


****  

| Synapse | Arrow | 
| --- | --- | 
| bit | TINYINT | 
| tinyint | SMALLINT | 
| smallint | SMALLINT | 
| int | INT | 
| bigint | BIGINT | 
| decimal | DECIMAL | 
| numeric | FLOAT8 | 
| smallmoney | FLOAT8 | 
| money | DECIMAL | 
| float\[24\] | FLOAT4 | 
| float\[53\] | FLOAT8 | 
| real | FLOAT4 | 
| datetime | Date\(MILLISECOND\) | 
| datetime2 | Date\(MILLISECOND\) | 
| smalldatetime | Date\(MILLISECOND\) | 
| date | Date\(DAY\) | 
| time | VARCHAR | 
| datetimeoffset | Date\(MILLISECOND\) | 
| char\[n\] | VARCHAR | 
| varchar\[n/max\] | VARCHAR | 
| nchar\[n\] | VARCHAR | 
| nvarchar\[n/max\] | VARCHAR | 

## Partitions and splits<a name="connectors-azure-synapse-partitions-and-splits"></a>

A partition is represented by a single partition column of type `varchar`\. Synapse supports range partitioning, so partitioning is implemented by extracting the partition column and partition range from Synapse metadata tables\. These range values are used to create the splits\.

## Performance<a name="connectors-azure-synapse-performance"></a>

The Lambda function performs predicate pushdown to decrease the data scanned by the query\. `LIMIT` clauses reduce the amount of data scanned, but if you do not provide a predicate, you should expect `SELECT` queries with a `LIMIT` clause to scan at least 16 MB of data\. Selecting a subset of columns significantly slows down query runtime\. The connector shows significant throttling due to concurrency\.

## License information<a name="connectors-synapse-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-synapse/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-synapse/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-synapse-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-synapse/pom.xml) file for the Synapse connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-synapse) on GitHub\.com\.