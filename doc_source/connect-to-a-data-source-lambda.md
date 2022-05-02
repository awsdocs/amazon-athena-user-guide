# Deploying a connector and connecting to a data source<a name="connect-to-a-data-source-lambda"></a>

Preparing to create federated queries is a two\-part process: deploying a Lambda function data source connector, and connecting the Lambda function to a data source\. In this process, you give the Lambda function a name that you can later choose in the Athena console and give the connector a name that you can reference in your SQL queries\.

**Note**  
To use the Athena Federated Query feature with AWS Secrets Manager, you must configure an Amazon VPC private endpoint for Secrets Manager\. For more information, see [Create a Secrets Manager VPC private endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint-create) in the *AWS Secrets Manager User Guide*\.

## Part 1: Deploying a data source connector<a name="connect-to-a-data-source-lambda-deploying"></a>

To choose, name, and deploy a data source connector, you use the Athena and Lambda consoles in an integrated process\.

**Note**  
 To use the Amazon Athena Federated Query feature, set your workgroup to Athena engine version 2\. For steps, see [Changing Athena engine versions](engine-versions-changing.md)\. 

**To deploy a data source connector**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Data sources**\.

1. On the **Data sources** page, choose **Create data source**\.

1. For **Choose a data source**, choose the data source that you want Athena to query, considering the following guidelines:
   + Choose a federated query option that corresponds to your data source\. Athena has prebuilt data source connectors that you can configure for sources including MySQL, Amazon DocumentDB, and PostgreSQL\.
   + Choose **S3 \- AWS Glue Data Catalog** if you want to query data in Amazon S3 and you are not using an Apache Hive metastore or one of the other federated query data source options on this page\. Athena uses the AWS Glue Data Catalog to store metadata and schema information for data sources in Amazon S3\. This is the default \(non\-federated\) option\. For more information, see [Using AWS Glue to connect to data sources in Amazon S3](data-sources-glue.md)\.
   + Choose **S3 \- Apache Hive metastore** to query data sets in Amazon S3 that use an Apache Hive metastore\. For more information about this option, see [Connecting Athena to an Apache Hive metastore](connect-to-data-source-hive-connecting-athena-to-an-apache-hive-metastore.md)\.
   + Choose **Custom or shared connector** if you want to create your own data source connector for use with Athena\. For information about writing a data source connector, see [Writing a data source connector using the Athena Query Federation SDK](connect-data-source-federation-sdk.md)\.

   This tutorial chooses **Amazon CloudWatch Logs** as the federated data source\.

1. Choose **Next**\.

1. On the **Enter data source details** page, for **Data source name**, enter the name that you want to use in your SQL statements when you query the data source from Athena \(for example, `CloudWatchLogs`\)\. The name can be up to 127 characters and must be unique within your account\. It cannot be changed after you create it\. Valid characters are a\-z, A\-Z, 0\-9, \_ \(underscore\), @ \(at sign\) and \- \(hyphen\)\. The names `awsdatacatalog`, `hive`, `jmx`, and `system` are reserved by Athena and cannot be used for data source names\. 

1. For **Lambda function**, choose **Create Lambda function**\. The function page for the connector that you chose opens in the AWS Lambda console\. The page includes detailed information about the connector\.

1. Under **Application settings**, read the description for each application setting carefully, and then enter values that correspond to your requirements\.

   The application settings that you see vary depending on the connector for your data source\. The minimum required settings include:
   + **AthenaCatalogName** – A name, in lower case, for the Lambda function that indicates the data source that it targets, such as `cloudwatchlogs`\.
   + **SpillBucket** – An Amazon S3 bucket in your account to store data that exceeds Lambda function response size limits\.
**Note**  
Spilled data is not reused in subsequent executions and can be safely deleted after 12 hours\. Athena does not delete this data for you\. To manage these objects, consider adding an object lifecycle policy that deletes old data from your Amazon S3 spill bucket\. For more information, see [Managing your storage lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) in the *Amazon S3 User Guide*\.

1. Select **I acknowledge that this app creates custom IAM roles and resource policies**\. For more information, choose the **Info** link\.

1. Choose **Deploy**\. When the deployment is complete, the Lambda function appears in the **Resources** section in the Lambda console\.

## Part 2: Connecting to the data source<a name="connect-to-a-data-source-lambda-connecting"></a>

After you deploy the data source connector to your account, you can connect Athena to it\.

**To connect Athena to a data source using a connector that you have deployed to your account**

1. Return to the **Enter data source details** page of the Athena console\.

1. In the **Connection details** section, choose the refresh icon next to the **Select or enter a Lambda function** search box\.

1. Choose the name of the function that you just created in the Lambda console\. The ARN of the Lambda function displays\.

1. \(Optional\) For **Tags**, add key\-value pairs to associate with this data source\. For more information about tags, see [Tagging Athena resources](tags.md)\.

1. Choose **Next**\.

1. On the **Review and create** page, review the data source details, and then choose **Create data source**\. 

1. The **Data source details** section of the page for your data source shows information about your new connector\. You can now use the connector in your Athena queries\. 

   For information about writing queries with data connectors, see [Writing federated queries](writing-federated-queries.md)\.