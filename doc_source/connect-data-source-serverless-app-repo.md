# Using the AWS Serverless Application Repository to deploy a data source connector<a name="connect-data-source-serverless-app-repo"></a>

To deploy a data source connector, you can use the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/) instead of starting with the Athena console\. Use the AWS Serverless Application Repository to find the connector that you want to use, provide the parameters that the connector requires, and then deploy the connector to your account\. Then, after you deploy the connector, you use the Athena console to make the data source available to Athena\.

## Deploying the connector to Your Account<a name="connect-data-source-serverless-app-repo-deploying"></a>

**To use the AWS Serverless Application Repository to deploy a data source connector to your account**

1. Sign in to the AWS Management Console and open the **Serverless App Repository**\.

1. In the navigation pane, choose **Available applications**\.

1. Select the option **Show apps that create custom IAM roles or resource policies**\.

1. In the search box, type the name of the connector\. For a list of prebuilt Athena data connectors, see [Available data source connectors](connectors-available.md)\.

1. Choose the name of the connector\. Choosing a connector opens the Lambda function's **Application details** page in the AWS Lambda console\.

1. On the right side of the details page, for **Application settings**, fill in the required information\. The minimum required settings include the following\. For information about the remaining configurable options for data connectors built by Athena, see the corresponding [Available connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) topic on GitHub\.
   + **AthenaCatalogName** – A name for the Lambda function in lower case that indicates the data source that it targets, such as `cloudwatchlogs`\.
   + **SpillBucket** – Specify an Amazon S3 bucket in your account to receive data from any large response payloads that exceed Lambda function response size limits\.

1. Select **I acknowledge that this app creates custom IAM roles and resource policies**\. For more information, choose the **Info** link\.

1. At the bottom right of the **Application settings** section, choose **Deploy\.** When the deployment is complete, the Lambda function appears in the **Resources** section in the Lambda console\.

## Making the connector available in Athena<a name="connect-data-source-serverless-app-repo-making-the-connector-available-in-athena"></a>

Now you are ready to use the Athena console to make the data source connector available to Athena\.

**To make the data source connector available to Athena**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Data sources**\.

1. On the **Data sources** page, choose **Create data source**\.

1. For **Choose a data source**, choose the data source for which you created a connector in the AWS Serverless Application Repository\. This tutorial uses **Amazon CloudWatch Logs** as the federated data source\.

1. Choose **Next**\.

1. On the **Enter data source details** page, for **Data source name**, enter the name that you want to use in your SQL statements when you query the data source from Athena \(for example, `CloudWatchLogs`\)\. The name can be up to 127 characters and must be unique within your account\. It cannot be changed after you create it\. Valid characters are a\-z, A\-Z, 0\-9, \_ \(underscore\), @ \(at sign\) and \- \(hyphen\)\. The names `awsdatacatalog`, `hive`, `jmx`, and `system` are reserved by Athena and cannot be used for data source names\. 

1. In the **Connection details** section, use the **Select or enter a Lambda function** box to choose the name of the function that you just created\. The ARN of the Lambda function displays\.

1. \(Optional\) For **Tags**, add key\-value pairs to associate with this data source\. For more information about tags, see [Tagging Athena resources](tags.md)\.

1. Choose **Next**\.

1. On the **Review and create** page, review the data source details, and then choose **Create data source**\. 

1. The **Data source details** section of the page for your data source shows information about your new connector\. You can now use the connector in your Athena queries\. 

   For information about using data connectors in queries, see [Running federated queries](running-federated-queries.md)\.