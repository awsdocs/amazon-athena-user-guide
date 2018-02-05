# Using Athena with the JDBC Driver<a name="connect-with-jdbc"></a>

You can use a JDBC connection to connect Athena to business intelligence tools, such as SQL Workbench\. To do this, download, install, and configure the Athena JDBC driver, using the following link on Amazon S3\.

## Download the JDBC Driver<a name="download-jdbc-ate-version"></a>

1. Download the driver \(JDBC 4\.1 and Java 8 compatible\) from this location in Amazon S3 [https://s3.amazonaws.com/athena-downloads/drivers/AthenaJDBC41-1.1.0.jar](https://s3.amazonaws.com/athena-downloads/drivers/AthenaJDBC41-1.1.0.jar)\.

1. Use the AWS CLI with the following command: 

   ```
   aws s3 cp s3://athena-downloads/drivers/AthenaJDBC41-1.1.0.jar [local_directory]
   ```

## Specify the Connection String<a name="ate-jdbc-connection-string"></a>

To specify the JDBC driver connection URL in your custom application, use the string in this format:

```
jdbc:awsathena://athena.{REGION}.amazonaws.com:443
```

where `{REGION}` is a region identifier, such as `us-west-2`\. For information on Athena regions see [Regions](http://docs.aws.amazon.com/general/latest/gr/rande.html#athena)\.

## Specify the JDBC Driver Class Name<a name="ate-jdbc-class-name"></a>

To use the driver in custom applications, set up your Java class path to the location of the JAR file that you downloaded from Amazon S3 [https://s3.amazonaws.com/athena-downloads/drivers/AthenaJDBC41-1.1.0.jar](https://s3.amazonaws.com/athena-downloads/drivers/AthenaJDBC41-1.1.0.jar) in the previous section\. This makes the classes within the JAR available for use\. The main JDBC driver class is `com.amazonaws.athena.jdbc.AthenaDriver`\.

## Provide the JDBC Driver Credentials<a name="ate-jdbc-credentials"></a>

To gain access to AWS services and resources, such as Athena and the Amazon S3 buckets, provide JDBC driver credentials to your application\.

 To provide credentials in the Java code for your application:

1. Use a class which implements the [http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/AWSCredentialsProvider.html](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/AWSCredentialsProvider.html)\. 

1. Set the JDBC property, `aws_credentials_provider_class`, equal to the class name, and include it in your classpath\.

1. To include constructor parameters, set the JDBC property `aws_credentials_provider_arguments` as specified in the following section about configuration options\.

Another method to supply credentials to BI tools, such as SQL Workbench, is to supply the credentials used for the JDBC as AWS access key and AWS secret key for the JDBC properties for user and password, respectively\.

Users who connect through the JDBC driver and have custom access policies attached to their profiles need permissions for policy actions in addition to those in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\.

### Policies<a name="jdbc-ate-version-policies"></a>

You must allow JDBC users to perform a set of policy\-specific actions\. These actions are not part of the Athena API\. If the following actions are not allowed, users will be unable to see databases and tables:

+ `athena:GetCatalogs`

+ `athena:GetExecutionEngine`

+ `athena:GetExecutionEngines`

+ `athena:GetNamespace`

+ `athena:GetNamespaces`

+ `athena:GetTable`

+ `athena:GetTables`

## Configure the JDBC Driver Options<a name="ate-jdbc-driver-options"></a>

You can configure the following options for the JDBC driver\. With this version of the driver, you can also pass parameters using the standard JDBC URL syntax, for example: `jdbc:awsathena://athena.us-west-1.amazonaws.com:443?max_error_retries=20&connection_timeout=20000`\.


**Options for the JDBC Driver**  

| Property Name | Description | Default Value | Is Required | 
| --- | --- | --- | --- | 
| s3\_staging\_dir | The S3 location to which your query output is written, for example s3://query\-results\-bucket/folder/, which is established under Settings in the Athena Console, [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\. The JDBC driver then asks Athena to read the results and provide rows of data back to the user\. | N/A | Yes | 
| query\_results\_encryption\_option | The encryption method to use for the directory specified by s3\_staging\_dir\. If not specified, the location is not encrypted\. Valid values are SSE\_S3, SSE\_KMS, and CSE\_KMS\. | N/A | No | 
| query\_results\_aws\_kms\_key |  The Key ID of the AWS customer master key \(CMK\) to use if` query_results_encryption_option` specifies `SSE-KMS` or `CSE-KMS`\. For example, `123abcde-4e56-56f7-g890-1234h5678i9j`\.  | N/A | No | 
| aws\_credentials\_provider\_class | The credentials provider class name, which implements the AWSCredentialsProvider interface\. | N/A | No | 
| aws\_credentials\_provider\_arguments | Arguments for the credentials provider constructor as comma\-separated values\. | N/A | No | 
| max\_error\_retries |  The maximum number of retries that the JDBC client attempts to make a request to Athena\.  | 10 | No | 
| connection\_timeout | The maximum amount of time, in milliseconds, to make a successful connection to Athena before an attempt is terminated\. | 10,000 | No | 
| socket\_timeoutt | The maximum amount of time, in milliseconds, to wait for a socket in order to send data to Athena\. | 10,000 | No | 
| retry\_base\_delay | Minimum delay amount, in milliseconds, between retrying attempts to connect Athena\. | 100 | No | 
| retry\_max\_backoff\_time | Maximum delay amount, in milliseconds, between retrying attempts to connect to Athena\. | 1000 | No | 
| log\_path | Local path of the Athena JDBC driver logs\. If no log path is provided, then no log files are created\. | N/A | No | 
| log\_level | Log level of the Athena JDBC driver logs\. Valid values: INFO, DEBUG, WARN, ERROR, ALL, OFF, FATAL, TRACE\. | N/A | No | 