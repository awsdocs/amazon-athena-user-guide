# Amazon Athena MSK connector<a name="connectors-msk"></a>

The Amazon Athena connector for [Amazon MSK](http://aws.amazon.com/msk/) enables Amazon Athena to run SQL queries on your Apache Kafka topics\. Use this connector to view [Apache Kafka](https://kafka.apache.org/) topics as tables and messages as rows in Athena\. For additional information, see [Analyze real\-time streaming data in Amazon MSK with Amazon Athena](http://aws.amazon.com/blogs/big-data/analyze-real-time-streaming-data-in-amazon-msk-with-amazon-athena/) in the AWS Big Data Blog\.

## Prerequisites<a name="connectors-msk-prerequisites"></a>

Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.

## Limitations<a name="connectors-msk-limitations"></a>
+ Write DDL operations are not supported\.
+ Any relevant Lambda limits\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\.
+ Date and timestamp data types in filter conditions must be cast to appropriate data types\.
+ Date and timestamp data types are not supported for the CSV file type and are treated as varchar values\.
+ Mapping into nested JSON fields is not supported\. The connector maps top\-level fields only\.
+ The connector does not support complex types\. Complex types are interpreted as strings\.
+ To extract or work with complex JSON values, use the JSON\-related functions available in Athena\. For more information, see [Extracting data from JSON](extracting-data-from-JSON.md)\.
+ The connector does not support access to Kafka message metadata\.

## Terms<a name="connectors-msk-terms"></a>
+ **Metadata handler** – A Lambda handler that retrieves metadata from your database instance\.
+ **Record handler** – A Lambda handler that retrieves data records from your database instance\.
+ **Composite handler** – A Lambda handler that retrieves both metadata and data records from your database instance\.
+ **Kafka endpoint** – A text string that establishes a connection to a Kafka instance\.

## Cluster compatibility<a name="connectors-msk-cluster-compatibility"></a>

The MSK connector can be used with the following cluster types\.
+ **MSK Provisioned cluster** – You manually specify, monitor, and scale cluster capacity\.
+ **MSK Serverless cluster** – Provides on\-demand capacity that scales automatically as application I/O scales\.
+ **Standalone Kafka** – A direct, unauthenticated connection to Kafka\.

## Supported authentication methods<a name="connectors-msk-supported-authentication-methods"></a>

The connector supports the following authentication methods\.
+ [SASL/IAM](https://docs.aws.amazon.com/msk/latest/developerguide/iam-access-control.html) 
+ [SSL](https://docs.aws.amazon.com/msk/latest/developerguide/msk-authentication.html)
+ [SASL/SCRAM](https://docs.aws.amazon.com/msk/latest/developerguide/msk-password.html)
+ SASL/PLAIN
+ SASL/PLAINTEXT
+ NO\_AUTH

  For more information, see [Configuring authentication for the Athena MSK connector](#connectors-msk-setup-configuring-authentication)\.

## Supported input data formats<a name="connectors-msk-supported-input-data-formats"></a>

The connector supports the following input data formats\.
+ JSON
+ CSV

## Parameters<a name="connectors-msk-parameters"></a>

Use the Lambda environment variables mentioned in this section to configure the Athena MSK connector\.
+ **auth\_type** – Specifies the authentication type of the cluster\. The connector supports the following types of authentication:
  + **NO\_AUTH** – Connect directly to Kafka with no authentication \(for example, to a Kafka cluster deployed over an EC2 instance that does not use authentication\)\.
  + **SASL\_SSL\_PLAIN** – This method uses the `SASL_SSL` security protocol and the `PLAIN` SASL mechanism\.
  + **SASL\_PLAINTEXT\_PLAIN** – This method uses the `SASL_PLAINTEXT` security protocol and the `PLAIN` SASL mechanism\.
**Note**  
The `SASL_SSL_PLAIN` and `SASL_PLAINTEXT_PLAIN` authentication types are supported by Apache Kafka but not by Amazon MSK\.
  + **SASL\_SSL\_AWS\_MSK\_IAM** – IAM access control for Amazon MSK enables you to handle both authentication and authorization for your MSK cluster\. Your user's AWS credentials \(secret key and access key\) are used to connect with the cluster\. For more information, see [IAM access control](https://docs.aws.amazon.com/msk/latest/developerguide/iam-access-control.html) in the Amazon Managed Streaming for Apache Kafka Developer Guide\.
  + **SASL\_SSL\_SCRAM\_SHA512** – You can use this authentication type to control access to your Amazon MSK clusters\. This method stores the user name and password on AWS Secrets Manager\. The secret must be associated with the Amazon MSK cluster\. For more information, see [Setting up SASL/SCRAM authentication for an Amazon MSK cluster](https://docs.aws.amazon.com/msk/latest/developerguide/msk-password.html#msk-password-tutorial) in the Amazon Managed Streaming for Apache Kafka Developer Guide\.
  + **SSL** – SSL authentication makes use of key store and trust store certificate to connect with Amazon MSK cluster\. The certificates credentials are stored in the AWS Secrets Manager the certificates are stored in the Amazon S3 bucket\. For more information, see [Mutual TLS authentication](https://docs.aws.amazon.com/msk/latest/developerguide/msk-authentication.html) in the Amazon Managed Streaming for Apache Kafka Developer Guide\.

    For more information, see [Configuring authentication for the Athena MSK connector](#connectors-msk-setup-configuring-authentication)\.
+ **certificates\_s3\_reference** – The Amazon S3 location that contains the certificates \(the key store and trust store files\)\.
+ **disable\_spill\_encryption** – \(Optional\) When set to `True`, disables spill encryption\. Defaults to `False` so that data that is spilled to S3 is encrypted using AES\-GCM – either using a randomly generated key or KMS to generate keys\. Disabling spill encryption can improve performance, especially if your spill location uses [server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html)\.
+ **kafka\_endpoint** – The endpoint details to provide to Kafka\. For example, for an Amazon MSK cluster, you provide a [bootstrap URL](https://docs.aws.amazon.com/msk/latest/developerguide/msk-get-bootstrap-brokers.html) for the cluster\.
+ **secrets\_manager\_secret** – The name of the AWS secret in which the credentials are saved\. This parameter is not required for IAM authentication\.
+ **Spill parameters** – Lambda functions temporarily store \("spill"\) data that do not fit into memory to Amazon S3\. All database instances accessed by the same Lambda function spill to the same location\. Use the parameters in the following table to specify the spill location\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/connectors-msk.html)

## Data type support<a name="connectors-msk-data-type-support"></a>

The following table shows the corresponding data types supported for Kafka and Apache Arrow\.


****  

| Kafka | Arrow | 
| --- | --- | 
| CHAR | VARCHAR | 
| VARCHAR | VARCHAR | 
| TIMESTAMP | MILLISECOND | 
| DATE | DAY | 
| BOOLEAN | BOOL | 
| SMALLINT | SMALLINT | 
| INTEGER | INT | 
| BIGINT | BIGINT | 
| DECIMAL | FLOAT8 | 
| DOUBLE | FLOAT8 | 

## Partitions and splits<a name="connectors-msk-partitions-and-splits"></a>

Kafka topics are split into partitions\. Each partition is ordered\. Each message in a partition has an incremental ID called an *offset*\. Each Kafka partition is further divided to multiple splits for parallel processing\. Data is available for the retention period configured in Kafka clusters\.

## Best practices<a name="connectors-msk-best-practices"></a>

As a best practice, use predicate pushdown when you query Athena, as in the following examples\.

```
SELECT * 
FROM "msk_catalog_name"."glue_schema_registry_name"."glue_schema_name" 
WHERE integercol = 2147483647
```

```
SELECT * 
FROM "msk_catalog_name"."glue_schema_registry_name"."glue_schema_name" 
WHERE timestampcol >= TIMESTAMP '2018-03-25 07:30:58.878'
```

## Setting up the MSK connector<a name="connectors-msk-setup"></a>

Before you can use the connector, you must set up your Amazon MSK cluster, use the [AWS Glue Schema Registry](https://docs.aws.amazon.com/glue/latest/dg/schema-registry.html) to define your schema, and configure authentication for the connector\.

**Note**  
If you deploy the connector into a VPC in order to access private resources and also want to connect to a publicly accessible service like Confluent, you must associate the connector with a private subnet that has a NAT Gateway\. For more information, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the Amazon VPC User Guide\.

When working with the AWS Glue Schema Registry, note the following points:
+ Make sure that the text in **Description** field of the AWS Glue Schema Registry includes the string `{AthenaFederationMSK}`\. This marker string is required for AWS Glue Registries that you use with the Amazon Athena MSK connector\.
+ For best performance, use only lowercase for your AWS Glue Registry and Schema names\. Using mixed casing causes the connector to perform a case insensitive search that is more computationally intensive\. 

**To set up your Amazon MSK environment and AWS Glue Schema Registry**

1. Set up your Amazon MSK environment\. For information and steps, see [Setting up Amazon MSK](https://docs.aws.amazon.com/msk/latest/developerguide/before-you-begin.html) and [Getting started using Amazon MSK](https://docs.aws.amazon.com/msk/latest/developerguide/getting-started.html) in the Amazon Managed Streaming for Apache Kafka Developer Guide\.

1. Upload the Kafka topic description file \(that is, its schema\) in JSON format to the AWS Glue Schema Registry\. For more information, see [Integrating with AWS Glue Schema Registry](https://docs.aws.amazon.com/glue/latest/dg/schema-registry-integrations.html) in the AWS Glue Developer Guide\. For example schemas, see the following section\.

### Schema examples for the AWS Glue Schema Registry<a name="connectors-msk-setup-schema-examples"></a>

Use the format of the examples in this section when you upload your schema to the [AWS Glue Schema Registry](https://docs.aws.amazon.com/glue/latest/dg/schema-registry.html)\.

#### JSON type schema example<a name="connectors-msk-setup-schema-examples-json"></a>

In the following example, the schema to be created in the AWS Glue Schema Registry specifies `json` as the value for `dataFormat` and uses `datatypejson` for `topicName`\.

**Note**  
The value for `topicName` should use the same casing as the topic name in Kafka\. 

```
{
  "topicName": "datatypejson",
  "message": {
    "dataFormat": "json",
    "fields": [
      {
        "name": "intcol",
        "mapping": "intcol",
        "type": "INTEGER"
      },
      {
        "name": "varcharcol",
        "mapping": "varcharcol",
        "type": "VARCHAR"
      },
      {
        "name": "booleancol",
        "mapping": "booleancol",
        "type": "BOOLEAN"
      },
      {
        "name": "bigintcol",
        "mapping": "bigintcol",
        "type": "BIGINT"
      },
      {
        "name": "doublecol",
        "mapping": "doublecol",
        "type": "DOUBLE"
      },
      {
        "name": "smallintcol",
        "mapping": "smallintcol",
        "type": "SMALLINT"
      },
      {
        "name": "tinyintcol",
        "mapping": "tinyintcol",
        "type": "TINYINT"
      },
      {
        "name": "datecol",
        "mapping": "datecol",
        "type": "DATE",
        "formatHint": "yyyy-MM-dd"
      },
      {
        "name": "timestampcol",
        "mapping": "timestampcol",
        "type": "TIMESTAMP",
        "formatHint": "yyyy-MM-dd HH:mm:ss.SSS"
      }
    ]
  }
}
```

#### CSV type schema example<a name="connectors-msk-setup-schema-examples-csv"></a>

In the following example, the schema to be created in the AWS Glue Schema Registry specifies `csv` as the value for `dataFormat` and uses `datatypecsvbulk` for `topicName`\. The value for `topicName` should use the same casing as the topic name in Kafka\.

```
{
  "topicName": "datatypecsvbulk",
  "message": {
    "dataFormat": "csv",
    "fields": [
      {
        "name": "intcol",
        "type": "INTEGER",
        "mapping": "0"
      },
      {
        "name": "varcharcol",
        "type": "VARCHAR",
        "mapping": "1"
      },
      {
        "name": "booleancol",
        "type": "BOOLEAN",
        "mapping": "2"
      },
      {
        "name": "bigintcol",
        "type": "BIGINT",
        "mapping": "3"
      },
      {
        "name": "doublecol",
        "type": "DOUBLE",
        "mapping": "4"
      },
      {
        "name": "smallintcol",
        "type": "SMALLINT",
        "mapping": "5"
      },
      {
        "name": "tinyintcol",
        "type": "TINYINT",
        "mapping": "6"
      },
      {
        "name": "floatcol",
        "type": "DOUBLE",
        "mapping": "7"
      }
    ]
  }
}
```

### Configuring authentication for the Athena MSK connector<a name="connectors-msk-setup-configuring-authentication"></a>

You can use a variety of methods to authenticate to your Amazon MSK cluster, including IAM, SSL, SCRAM, and standalone Kafka\. The standalone Kafka connection is unauthenticated and does not require configuration\.

The following table shows the authentication types for the connector and the security protocol and SASL mechanism for each\. For more information, see [Authentication and authorization for Apache Kafka APIs](https://docs.aws.amazon.com/msk/latest/developerguide/kafka_apis_iam.html) in the Amazon Managed Streaming for Apache Kafka Developer Guide\.


****  

| auth\_type | security\.protocol | sasl\.mechanism | 
| --- | --- | --- | 
| SASL\_SSL\_PLAIN | SASL\_SSL | PLAIN | 
| SASL\_PLAINTEXT\_PLAIN | SASL\_PLAINTEXT | PLAIN | 
| SASL\_SSL\_AWS\_MSK\_IAM | SASL\_SSL | AWS\_MSK\_IAM | 
| SASL\_SSL\_SCRAM\_SHA512 | SASL\_SSL | SCRAM\-SHA\-512 | 
| SSL | SSL | N/A | 

**Note**  
The `SASL_SSL_PLAIN` and `SASL_PLAINTEXT_PLAIN` authentication types are supported by Apache Kafka but not by Amazon MSK\.

#### SASL/IAM<a name="connectors-msk-setup-configuring-authentication-sasl-iam"></a>

If the cluster uses IAM authentication, you must configure the IAM policy for the user when you set up the cluster\. For more information, see [IAM access control](https://docs.aws.amazon.com/msk/latest/developerguide/IAM-access-control.html) in the Amazon Managed Streaming for Apache Kafka Developer Guide\.

To use this authentication type, set the `auth_type` Lambda environment variable for the connector to `SASL_SSL_AWS_MSK_IAM`\. 

#### SSL<a name="connectors-msk-setup-configuring-authentication-tls"></a>

If the cluster is SSL authenticated, you must generate the trust store and key store files and upload them to the Amazon S3 bucket\. You must provide this Amazon S3 reference when you deploy the connector\. The key store, trust store, and SSL key are stored in the AWS Secrets Manager\. You provide the AWS secret key when you deploy the connector\.

For information on creating a secret in Secrets Manager, see [Create an AWS Secrets Manager secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/create_secret.html)\.

To use this authentication type, set the environment variables as shown in the following table\.


****  

| Parameter | Value | 
| --- | --- | 
| auth\_type | SSL | 
| certificates\_s3\_reference | The Amazon S3 location that contains the certificates\. | 
| secrets\_manager\_secret | The name of your AWS secret key\. | 

After you create a secret in Secrets Manager, you can view it in the Secrets Manager console\.

**To view your secret in Secrets Manager**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the navigation pane, choose **Secrets**\.

1. On the **Secrets** page, choose the link to your secret\.

1. On the details page for your secret, choose **Retrieve secret value**\.

   The following image shows an example secret with three key/value pairs: `keystore_password`, `truststore_password`, and `ssl_key_password`\.  
![\[Retrieving an SSL secret in Secrets Manager\]](http://docs.aws.amazon.com/athena/latest/ug/images/connectors-msk-setup-1.png)

#### SASL/SCRAM<a name="connectors-msk-setup-configuring-authentication-sasl-scram"></a>

If your cluster uses SCRAM authentication, provide the Secrets Manager key that is associated with the cluster when you deploy the connector\. The user's AWS credentials \(secret key and access key\) are used to authenticate with the cluster\.

Set the environment variables as shown in the following table\.


****  

| Parameter | Value | 
| --- | --- | 
| auth\_type | SASL\_SSL\_SCRAM\_SHA512 | 
| secrets\_manager\_secret | The name of your AWS secret key\. | 

The following image shows an example secret in the Secrets Manager console with two key/value pairs: one for `username`, and one for `password`\.

![\[Retrieving a SCRAM secret in Secrets Manager\]](http://docs.aws.amazon.com/athena/latest/ug/images/connectors-msk-setup-2.png)

## License information<a name="connectors-msk-license-information"></a>

By using this connector, you acknowledge the inclusion of third party components, a list of which can be found in the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-msk/pom.xml) file for this connector, and agree to the terms in the respective third party licenses provided in the [LICENSE\.txt](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-msk/LICENSE.txt) file on GitHub\.com\.

## See also<a name="connectors-msk-see-also"></a>

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-msk) on GitHub\.com\.