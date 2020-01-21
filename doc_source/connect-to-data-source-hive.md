# Using Athena Data Connector for External Hive Metastore \(Preview\)<a name="connect-to-data-source-hive"></a>

You can use the Athena Data Connector for External Hive Metastore \(Preview\) to query data sets in Amazon S3 that use an Apache Hive metastore\. No migration of metadata to the AWS Glue Data Catalog is necessary\. In the Athena management console, you configure a Lambda function to communicate with the Hive metastore in your private VPC and then connect it\. For the Lambda function code, you can use Athena's default implementation – the Athena data source connector for external Hive metastore – or provide your own\.

## Considerations and Limitations<a name="connect-to-a-data-source-hive-considerations"></a>

Prebuilt and custom data connectors might require access to the following resources to function correctly\. Check the information for the connector that you use to ensure that you have configured your VPC correctly\. For information about required IAM permissions to run queries and create a data source connector in Athena, see [Allow Access to an Athena Data Connector for External Hive Metastore \(Preview\)](hive-metastore-iam-access.md)\.
+ **AmazonAthenaPreviewFunctionality workgroup** – To use this feature in preview, you must create an Athena workgroup named `AmazonAthenaPreviewFunctionality` and join that workgroup\. For more information, see [Managing Workgroups](workgroups-create-update-delete.md)\. 
+ **Amazon S3** – In addition to writing query results to the Athena query results location in Amazon S3, data connectors also write to a spill bucket in Amazon S3\. Connectivity and permissions to this Amazon S3 location are required\.
+ **Athena** – For checking query status and preventing overscan\.
+ **AWS Secrets Manager**
+ **AWS Glue** if your connector uses AWS Glue for supplemental or primary metadata\.
+ **AWS Key Management Service**
+ **Policies** – Hive metastore, Athena Query Federation, and UDFs require policies in addition to the [AmazonAthenaFullAccess Managed Policy](amazonathenafullaccess-managed-policy.md)\. For more information, see [Identity and Access Management in Athena](identity-and-access-management-in-athena.md)\.

## Connecting Athena to an Apache Hive Metastore<a name="connect-to-data-source-hive-connecting-athena-to-an-apache-hive-metastore"></a>

To connect Athena to an Apache Hive Metastore, you must create and configure a Lambda function\. For a basic implementation, you can perform all required steps from the Athena management console\.

**To connect Athena to a Hive metastore**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Connect data source**\.

1. On the **Connect data source** page, for **Choose a metadata catalog**, choose **Apache Hive metastore**\.

1. Choose **Next**\.

1. On the **Connection details** page, for **Lambda function**, click **Create Lambda function**\.

1. In the **Create Lambda function** dialog box, enter the following information for the Lambda function\. To use the default implementation, accept the defaults for the function code location in Amazon S3 and the Lambda handler\.
   + **Lambda function name** – Provide a name for the function\. For example, **myHiveMetastore**\.
   + **Lambda execution role** – Choose an IAM role or click **Create a new role** to create one\.
   + **Function code** – The location in Amazon S3 for the Lambda function JAR file\. Use the default or enter the location of your custom JAR file\.
   + **Lambda handler** – The method in the JAR file that implements the Hive connector\. Use the default, or replace it with the handler in your custom code\.
   + **Hive metastore \(HMS\) URI** – Enter the name of your Hive metastore host that uses the Thrift protocol at port 9083 with the syntax `thrift://<host_name>:9083`\.
   + **Spill location in S3** – Specify an Amazon S3 location in this account to hold spillover metadata if the Lambdafunction reponse size exceeds 4MB\.
   + **Virtual Private \(VPC\)** – Choose the VPC that contains your Hive metastore\.
   + **Subnets** – Choose the VPC subnets for Lambda to use to set up your VPC configuration\.
   + **Security Groups** – Choose the VPC security groups for Lambda to use to set up your VPC configuration\.
   + **Memory** – Specify a value from 128MB to 3008MB\. The Lambda function is allocated CPU cycles proportional to the amount of memory that you configure\.
   + **Timeout** – Specify a value from 1 second to 15 minutes 59 seconds\. The default is 3 seconds\.

1. Click **Create**\. The **Connection details** page informs you that the function is being created\. When the operation completes, your function name is available in the **Choose a function name** box, and your Lambda function ARN is displayed\.

1. For **Catalog name**, enter a unique name to use for the data source in your SQL queries\. The name can be up to 127 characters and must be unique within your account\. It cannot be changed after creation\. Valid characters are a\-z, A\-z, 0\-9, \_\(underscore\), @\(ampersand\) and \-\(hyphen\)\.

1. Click **Connect** to connect Athena to your data source\.

   The **Data sources** page shows your connector in the list of catalog names\. You can now use the **Catalog name** that you specified to reference the Hive metastore in your SQL queries\. Use the syntax in the following example, where `MyHiveMetastore` is the catalog name that you specified earlier\.

   ```
   SELECT * FROM MyHiveMetastore.CustomerData.customers; 
   ```