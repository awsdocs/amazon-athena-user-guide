# Amazon Athena Teradata connector<a name="connectors-teradata"></a>

 The Amazon Athena connector for Teradata enables Athena to run SQL queries on data stored in your Teradata databases\. 

## Prerequisites<a name="connectors-teradata-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.
+ Set up a VPC and a security group before you use this connector\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Limitations<a name="connectors-teradata-limitations"></a>
+ Write DDL operations are not supported\.
+ In a multiplexer setup, the spill bucket and prefix are shared across all database instances\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.

## Terms<a name="connectors-teradata-terms"></a>

The following terms relate to the Teradata connector\.
+ **Database instance** – Any instance of a database deployed on premises, on Amazon EC2, or on Amazon RDS\.
+ **Handler** – A Lambda handler that accesses your database instance\. A handler can be for metadata or for data records\.
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Property or parameter** – A database property used by handlers to extract database information\. You configure these properties as Lambda environment variables\.
+ **Connection String** – A string of text used to establish a connection to a database instance\.
+ **Catalog** – A non\-AWS Glue catalog registered with Athena that is a required prefix for the `connection_string` property\.
+ **Multiplexing handler** – A Lambda handler that can accept and use multiple database connections\.

## Lambda layer prerequisite<a name="connectors-teradata-layer-steps"></a>

To use the Teradata connector with Athena, you must create a Lambda layer that includes the Teradata JDBC driver\. A Lambda layer is a `.zip` file archive that contains additional code for a Lambda function\. When you deploy the Teradata connector to your account, you specify the layer's ARN\. This attaches the Lambda layer with the Teradata JDBC driver to the Teradata connector so that you can use it with Athena\.

For more information about Lambda layers, see [Creating and sharing Lambda layers](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html) in the *AWS Lambda Developer Guide*\.

**To create a Lambda layer for the teradata connector**

1. Browse to the Teradata JDBC driver download page at [https://downloads\.teradata\.com/download/connectivity/jdbc\-driver](https://downloads.teradata.com/download/connectivity/jdbc-driver)\.

1. Download the Teradata JDBC driver\. The website requires you to create an account and accept a license agreement to download the file\.

1. Extract the `terajdbc4.jar` file from the archive file that you downloaded\.

1. Create the following folder structure and place the `.jar` file in it\.

   `java\lib\terajdbc4.jar`

1. Create a `.zip` file of the entire folder structure that contains the `terajdbc4.jar` file\.

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. In the navigation pane, choose **Layers**, and then choose **Create layer**\.

1. For **Name**, enter a name for the layer \(for example, `TeradataJava11LambdaLayer`\)\.

1. Ensure that the **Upload a \.zip file** option is selected\.

1. Choose **Upload**, and then upload the zipped folder that contains the Teradata JDBC driver\.

1. Choose **Create**\.

1. On the details page for the layer, copy the layer ARN by choosing the clipboard icon at the top of the page\.

1. Save the ARN for reference\.

## Parameters<a name="connectors-teradata-parameters"></a>

Use the Lambda environment variables in this section to configure the Teradata connector\.

### Connection string<a name="connectors-teradata-connection-string"></a>

Use a JDBC connection string in the following format to connect to a database instance\.

```
teradata://${jdbc_connection_string}
```

### Using a multiplexing handler<a name="connectors-teradata-using-a-multiplexing-handler"></a>

You can use a multiplexer to connect to multiple database instances with a single Lambda function\. Requests are routed by catalog name\. Use the following classes in Lambda\.


****  

| Handler | Class | 
| --- | --- | 
| Composite handler | TeradataMuxCompositeHandler | 
| Metadata handler | TeradataMuxMetadataHandler | 
| Record handler | TeradataMuxRecordHandler | 

#### Multiplexing handler parameters<a name="connectors-teradata-multiplexing-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| $catalog\_connection\_string | Required\. A database instance connection string\. Prefix the string with the name of the catalog used in Athena\. For example, if the catalog registered with Athena is myteradatacatalog, then the environment variable name is myteradatacatalog\_connection\_string\. | 
| default | Required\. The default connection string\. This string is used when the catalog is lambda:$\{AWS\_LAMBDA\_FUNCTION\_NAME\}\. | 

The following example properties are for a Teradata MUX Lambda function that supports two database instances: `teradata1` \(the default\), and `teradata2`\.


****  

| Property | Value | 
| --- | --- | 
| default | teradata://jdbc:teradata://teradata2\.host/TMODE=ANSI,CHARSET=UTF8,DATABASE=TEST,user=sample2&password=sample2 | 
| teradata\_catalog1\_connection\_string | teradata://jdbc:teradata://teradata1\.host/TMODE=ANSI,CHARSET=UTF8,DATABASE=TEST,$\{Test/RDS/Teradata1\} | 
| teradata\_catalog2\_connection\_string | teradata://jdbc:teradata://teradata2\.host/TMODE=ANSI,CHARSET=UTF8,DATABASE=TEST,user=sample2&password=sample2 | 

#### Providing credentials<a name="connectors-teradata-providing-credentials"></a>

To provide a user name and password for your database in your JDBC connection string, you can use connection string properties or AWS Secrets Manager\.
+ **Connection String** – A user name and password can be specified as properties in the JDBC connection string\.
+ **AWS Secrets Manager** – To use the Athena Federated Query feature with AWS Secrets Manager, the VPC connected to your Lambda function should have [internet access](http://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/) or a [VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) to connect to Secrets Manager\.

  You can put the name of a secret in AWS Secrets Manager in your JDBC connection string\. The connector replaces the secret name with the `username` and `password` values from Secrets Manager\.

  For Amazon RDS database instances, this support is tightly integrated\. If you use Amazon RDS, we highly recommend using AWS Secrets Manager and credential rotation\. If your database does not use Amazon RDS, store the credentials as JSON in the following format:

  ```
  {"username": "${username}", "password": "${password}"}
  ```

**Example connection string with secret name**  
The following string has the secret name `${Test/RDS/Teradata1}`\.

```
teradata://jdbc:teradata1.host/TMODE=ANSI,CHARSET=UTF8,DATABASE=TEST,${Test/RDS/Teradata1}&...
```

The connector uses the secret name to retrieve secrets and provide the user name and password, as in the following example\.

```
teradata://jdbc:teradata://teradata1.host/TMODE=ANSI,CHARSET=UTF8,DATABASE=TEST,...&user=sample2&password=sample2&...
```

Currently, Teradata recognizes the `user` and `password` JDBC properties\. It also accepts the user name and password in the format *username*`/`*password* without the keys `user` or `password`\.

### Using a single connection handler<a name="connectors-teradata-using-a-single-connection-handler"></a>

You can use the following single connection metadata and record handlers to connect to a single Teradata instance\.


****  

| Handler type | Class | 
| --- | --- | 
| Composite handler | TeradataCompositeHandler | 
| Metadata handler | TeradataMetadataHandler | 
| Record handler | TeradataRecordHandler | 

#### Single connection handler parameters<a name="connectors-teradata-single-connection-handler-parameters"></a>


****  

| Parameter | Description | 
| --- | --- | 
| default | Required\. The default connection string\. This string is used when a catalog is not recognized\. | 

The single connection handlers support one database instance and must provide a `default` connection string parameter\. All other connection strings are ignored\.

The following example property is for a single Teradata instance supported by a Lambda function\.


****  

| Property | Value | 
| --- | --- | 
| default | teradata://jdbc:teradata://teradata1\.host/TMODE=ANSI,CHARSET=UTF8,DATABASE=TEST,secret=Test/RDS/Teradata1 | 

### Spill parameters<a name="connectors-teradata-spill-parameters"></a>

The Lambda SDK can spill data to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\.


****  

| Parameter | Description | 
| --- | --- | 
| spill\_bucket | Required\. Spill bucket name\. | 
| spill\_prefix | Required\. Spill bucket key prefix\. | 
| spill\_put\_request\_headers | \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 putObject request that is used for spilling \(for example, \{"x\-amz\-server\-side\-encryption" : "AES256"\}\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the Amazon Simple Storage Service API Reference\. | 

## Data type support<a name="connectors-teradata-data-type-support"></a>

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

## Partitions and splits<a name="connectors-teradata-partitions-and-splits"></a>

A partition is represented by a single partition column of type `Integer`\. The column contains partition names of the partitions defined on a Teradata table\. For a table that does not have partition names, \* is returned, which is equivalent to a single partition\. A partition is equivalent to a split\.


****  

| Name | Type | Description | 
| --- | --- | --- | 
| partition | Integer | Named partition in Teradata\. | 

## Performance tuning<a name="connectors-teradata-performance-tuning"></a>

Teradata supports native partitions\. The Athena Lambda connector can retrieve data from these partitions in parallel\. If you want to query very large datasets with uniform partition distribution, native partitioning is highly recommended\.

The Lambda function performs predicate pushdown to decrease the data scanned by the query\. `LIMIT` clauses reduce the amount of data scanned, but if you do not provide a predicate, you should expect `SELECT` queries with a `LIMIT` clause to scan at least 16 MB of data\. Selecting a subset of columns significantly slows down query runtime\. The connector shows some throttling due to concurrency\.

## License information<a name="connectors-teradata-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-teradata/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-teradata/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-teradata-see-also"></a>

For the latest JDBC driver version information, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-teradata/pom.xml) file for the Teradata connector on GitHub\.com\.

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-teradata) on GitHub\.com\.