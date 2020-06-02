# Reference Implementation<a name="datastores-hive-reference-implementation"></a>

Athena provides a reference implementation of its connector for external Hive metastore on GitHub\.com at [https://github\.com/awslabs/aws\-athena\-hive\-metastore](https://github.com/awslabs/aws-athena-hive-metastore)\.

The reference implementation is an [Apache Maven](https://maven.apache.org/) project that has the following modules:
+ `hms-service-api` – Contains the API operations between the Lambda function and the Athena service clients\. These API operations are defined in the `HiveMetaStoreService` interface\. Because this is a service contract, you should not change anything in this module\.
+ `hms-lambda-handler` – A set of default Lambda handlers that process all Hive metastore API calls\. The class `MetadataHandler` is the dispatcher for all API calls\. You do not need to change this package\.
+ `hms-lambda-layer` – A Maven assembly project that puts `hms-service-api`, `hms-lambda-handler`, and their dependencies into a `.zip` file\. The `.zip` file is registered as a Lambda layer for use by multiple Lambda functions\.
+ `hms-lambda-func` – An example Lambda function that has the following components\.
  + `HiveMetaStoreLambdaFunc` – An example Lambda function that extends `MetadataHandler`\.
  + `ThriftHiveMetaStoreClient` – A Thrift client that communicates with Hive metastore\. This client is written for Hive 2\.3\.0\. If you use a different Hive version, you might need to update this class to ensure that the response objects are compatible\.
  + `ThriftHiveMetaStoreClientFactory` – Controls the behavior of the Lambda function\. For example, you can provide your own set of handler providers by overriding the `getHandlerProvider()` method\.
  + `hms.properties` – Configures the Lambda function\. Most cases require updating the following two properties only\.
    + `hive.metastore.uris` – the URI of the Hive metastore in the format `thrift://<host_name>:9083`\.
    + `hive.metastore.response.spill.location`: The Amazon S3 location to store response objects when their sizes exceed a given threshold \(for example, 4MB\)\. The threshold is defined in the property `hive.metastore.response.spill.threshold`\. Changing the default value is not recommended\.
**Note**  
These two properties can be overridden by the [Lambda environment variables](https://docs.aws.amazon.com/lambda/latest/dg/env_variables.html) `HMS_URIS` and `SPILL_LOCATION`\. Use these variables instead of recompiling the source code for the Lambda function when you want to use the function with a different Hive metastore or spill location\.

## Building the Artifacts Yourself<a name="datastores-hive-reference-implementation-building-the-artifacts-yourself"></a>

Most use cases do not require you to modify the reference implementation\. However, if necessary, you can modify the source code, build the artifacts yourself, and upload them to an Amazon S3 location\.

Before you build the artifacts, update the properties `hive.metastore.uris` and `hive.metastore.response.spill.location` in the `hms.properties` file in the `hms-lambda-func` module\.

To build the artifacts, you must have Apache Maven installed and run the command `mvn install`\. This generates the layer `.zip` file in the output folder called `target` in the module `hms-lambda-layer` and the Lambda function `.jar` file in the module `hms-lambd-func`\.