# Deploying a Connector and Connecting to a Data Source<a name="connect-to-a-data-source-lambda"></a>

Preparing to create federated queries is a two\-part process: deploying a Lambda function data source connector, and connecting the Lambda function to a data source\. In the first part, you give the Lambda function a name that you can later choose in the Athena console\. In the second part, you give the connector a name that you can reference in your SQL queries\.

## Part 1: Deploying a Data Source Connector<a name="connect-to-a-data-source-lambda-deploying"></a>

To choose, name, and deploy a data source connector, you use the Athena and Lambda consoles in an integrated process\.

**Note**  
 To use the Amazon Athena Federated Query feature, set your workgroup to Athena engine version 2\. For steps, see [Changing Athena Engine Versions](engine-versions-changing.md)\. 

**To deploy a data source connector**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Do one of the following:
   + In the Query Editor navigation pane, choose **Connect data source**\.  
![\[Choose Connect data source in the navigation pane.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-a-data-source-nav-pane.png)
   + Choose the **Data sources** tab, and then choose **Connect data source**\.  
![\[On the Data sources tab, choose Connect data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-a-data-source-data-sources-tab.png)

1. On the **Connect data source** page, choose **Query a data source**\.

1. For **Choose a data source**, choose the data source that you want to query with Athena, such as **Amazon CloudWatch Logs**\.

1. Choose **Next**\.

1. For **Lambda function**, choose **Configure new function**\. The function page for the connector that you chose opens in the Lambda console\. The page includes detailed information about the connector\.

1. Under **Application settings**, enter the required information\. At a minimum, this includes:
   + **AthenaCatalogName** – A name for the Lambda function that indicates the data source that it targets, such as `cloudwatchlogs`\.
   + **SpillBucket** – An Amazon S3 bucket in your account to store data that exceeds Lambda function response size limits\.

1. Select **I acknowledge that this app creates custom IAM roles**\. For more information, choose the **Info** link\.

1. Choose **Deploy**\. The **Resources** section of the Lambda console shows the deployment status of the connector and informs you when the deployment is complete\.

## Part 2: Connecting to a Data Source<a name="connect-to-a-data-source-lambda-connecting"></a>

After you deploy the data source connector to your account, you can connect it to a data source\.

**To connect to a data source using a connector that you have deployed to your account**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Connect data source**\.

   Do one of the following:
   + In the Query Editor navigation pane, choose **Connect data source**\.
   + Choose the **Data sources** tab, and then choose **Connect data source**\.

1. Choose **Query a data source**\.

1. Choose the data source for the connector that you just deployed, such as **Amazon CloudWatch Logs**\. If you used the Athena Query Federation SDK to create your own connector and have deployed it to your account, choose **All other data sources**\.

1. Choose **Next**\.

1. For **Choose Lambda function**, choose the function that you named\. The Lambda function's ARN is displayed\.

1. For **Catalog name**, enter a unique name to use for the data source in your SQL queries, such as `cloudwatchlogs`\. The name can be up to 127 characters and must be unique within your account\. It cannot be changed after creation\. Valid characters are a\-z, A\-z, 0\-9, \_\(underscore\), @\(ampersand\) and \-\(hyphen\)\. The names `awsdatacatalog`, `hive`, `jmx`, and `system` are reserved by Athena and cannot be used for custom catalog names\. 

1. Choose **Connect**\. The **Data sources** page now shows your connector in the list of catalog names\. You can now use the connector in your queries\. 

   For information about writing queries with data connectors, see [Writing Federated Queries](writing-federated-queries.md)\.