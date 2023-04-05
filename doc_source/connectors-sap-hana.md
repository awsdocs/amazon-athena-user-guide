# Amazon Athena SAP HANA connector<a name="connectors-sap-hana"></a>

## Prerequisites<a name="connectors-saphana-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a data source connector](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-sap-hana-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.
+ In SAP HANA, object names are converted to uppercase when they are stored in the SAP HANA database\. However, because names in quotation marks are case sensitive, it is possible for two tables to have the same name in lower and upper case \(for example, `EMPLOYEE` and `employee`\)\.

  In Athena Federated Query, schema table names are provided to the Lambda function in lower case\. To work around this issue, you can provide `@schemaCase` query hints to retrieve the data from the tables that have case sensitive names\. Following are two sample queries with query hints\.

  ```
  SELECT * 
  FROM "lambda:saphanaconnector".SYSTEM."MY_TABLE@schemaCase=upper&tableCase=upper"
  ```

  ```
  SELECT * 
  FROM "lambda:saphanaconnector".SYSTEM."MY_TABLE@schemaCase=upper&tableCase=lower"
  ```

## Terms<a name="connectors-sap-hana-terms"></a>

The following terms relate to the SAP HANA connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Parameters<a name="connectors-sap-hana-parameters"></a>

Use the Lambda environment variables in this section to configure the SAP HANA connector\.

### Connection string<a name="connectors-sap-hana-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
saphana://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-sap-hana-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | SaphanaMuxCompositeHandler | 
| Metadata handler | SaphanaMuxMetadataHandler | 
| Record handler | SaphanaMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-sap-hana-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the environment variable with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is mysaphanacatalog, then the environment variable name is mysaphanacatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a Saphana MUX Lambda function that supports two database instances: `saphana1` \(the default\), and `saphana2`\.


****  

| Property | Value | 
| --- | --- | 
| default | saphana://jdbc:sap://saphana1\.host:port/?$\{Test/RDS/ Saphana1\} | 
| saphana\_catalog1\_connection\_string | saphana://jdbc:sap://saphana1\.host:port/?$\{Test/RDS/ Saphana1\} | 
| saphana\_catalog2\_connection\_string | saphana://jdbc:sap://saphana2\.host:port/?user=sample2&password=sample2 | 

#### Providing credentials<a name="connectors-sap-hana-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${Test/RDS/Saphana1}`\.

```
saphana://jdbc:sap://saphana1.host:port/?${Test/RDS/Saphana1}&...
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
saphana://jdbc:sap://saphana1.host:port/?user=sample2&password=sample2&...
```

Currently, the SAP HANA connector recognizes the `user` and `password` JDBC properties\.

### Using a single connection handler<a name="connectors-sap-hana-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single SAP HANA instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | SaphanaCompositeHandler | 
| Metadata handler | SaphanaMetadataHandler | 
| Record handler | SaphanaRecordHandler | 

#### Single connection handler parameters<a name="connectors-sap-hana-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single SAP HANA instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | saphana://jdbc:sap://saphana1\.host:port/?secret=Test/RDS/Saphana1 | 

### Spill parameters<a name="connectors-sap-hana-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-sap-hana-data-type-support"></a>

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

## Data type conversions<a name="connectors-sap-hana-data-type-conversions"></a>

In addition to the JDBC to Arrow conversions, the connector performs certain other conversions to make the SAP HANA source and Athena data types compatible\. These conversions help ensure that queries get executed successfully\. The following table shows these conversions\.


****  

| Source data type \(SAP HANA\) | Converted data type \(Athena\) | 
| --- | --- | 
| DECIMAL | BIGINT | 
| INTEGER | INT | 
| DATE | DATEDAY | 
| TIMESTAMP | DATEMILLI | 

All other unsupported data types are converted to `VARCHAR`\.

## Partitions and splits<a name="connectors-sap-hana-partitions-and-splits"></a>

A partition is represented by a single partition column of type `Integer`\. The column contains partition names of the partitions defined on an SAP HANA table\. For a table that does not have partition names, \* is returned, which is equivalent to a single partition\. A partition is equivalent to a split\.


****  

| Name | Type | Description | 
| --- | --- | --- | 
| PART\_ID | Integer | Named partition in SAP HANA\. | 

## Performance tuning<a name="connectors-sap-hana-performance-tuning"></a>

SAP HANA supports native partitions\. The Athena Lambda connector can retrieve data from these partitions in parallel\. If you want to query very large datasets with uniform partition distribution, native partitioning is highly recommended\.

The Lambda function performs predicate pushdown to decrease the data scanned by the query\. `LIMIT` clauses reduce the amount of data scanned, but if you do not provide a predicate, you should expect `SELECT` queries with a `LIMIT` clause to scan at least 16 MB of data\. Selecting a subset of columns significantly speeds up query runtime and reduces data scanned\.

The connector shows significant throttling, and sometimes query failures, due to concurrency\.

## License information<a name="connectors-saphana-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-saphana/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-saphana/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-saphana-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-saphana/pom.xml) file for the SAP HANA connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-saphana) on GitHub\.com\.