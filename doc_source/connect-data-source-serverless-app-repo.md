# Using the AWS Serverless Application Repository to Deploy a Data Source Connector<a name="connect-data-source-serverless-app-repo"></a>

To deploy a data source connector, you can use the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/) instead of starting with the Athena console\. Use the AWS Serverless Application Repository to find the connector that you want to use, provide the parameters that the connector requires, and then deploy the connector to your account\. Then, after you deploy the connector, you use the Athena console to make the data source available to Athena\.

**Note**  
To use the Amazon Athena Federated Query feature, set your workgroup to Athena engine version 2\. For steps, see [Changing Athena Engine Versions](engine-versions-changing.md)\.

**To use the AWS Serverless Application Repository to deploy a data source connector to your account**

1. Sign in to the AWS Management Console and open the **Serverless App Repository**\.

1. In the navigation pane, choose **Available applications**\.

1. Select the option **Show apps that create custom IAM roles or resource policies**\.

1. In the search box, type the name of the connector\. The author name **Amazon Athena Federation** is reserved for applications that the Amazon Athena team has written, tested, and validated\. For a list of prebuilt Athena data connectors, see [Using Athena Data Source Connectors](athena-prebuilt-data-connectors.md)\.

1. Choose the name of the connector\. Choosing a connector opens the Lambda function's **Application details** page in the AWS Lambda console\.

1. On the right side of the details page, for **Application settings**, fill in the required information\. The minimum required settings include the following\. For information about the remaining configurable options for data connectors built by Athena, see the corresponding [Available Connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) topic on GitHub\.
   + **AthenaCatalogName** – A name for the Lambda function in lower case that indicates the data source that it targets, such as `cloudwatchlogs`\.
   + **SpillBucket** – Specify an Amazon S3 bucket in your account to receive data from any large response payloads that exceed Lambda function response size limits\.

1. Select **I acknowledge that this app creates custom IAM roles and resource policies**\. For more information, choose the **Info** link\.

1. At the bottom right of the **Application settings** section, choose **Deploy\.**

1. Use the Athena console to make the data source available to Athena\. For steps, see [Part 2: Connecting to the Data Source](connect-to-a-data-source-lambda.md#connect-to-a-data-source-lambda-connecting)\.