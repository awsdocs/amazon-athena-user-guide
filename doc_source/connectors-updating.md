# Updating a data source connector<a name="connectors-updating"></a>

Athena recommends that you regularly update the data source connectors that you use to the latest version to take advantage of new features and enhancements\. To get started, you must find the latest version number\.

## Finding the latest Athena Query Federation version<a name="connectors-updating-finding-the-latest-version"></a>

The latest version number of Athena data source connectors corresponds to the latest Athena Query Federation version\. In certain cases, the GitHub releases can be slightly newer than what is available on the AWS Serverless Application Repository \(SAR\)\.

**To find the latest Athena Query Federation version number**

1. Visit the GitHub URL [https://github\.com/awslabs/aws\-athena\-query\-federation/releases/latest](https://github.com/awslabs/aws-athena-query-federation/releases/latest)\.

1. Note the release number in the main page heading in the following format:

   **Release v** *year*\.*week\_of\_year*\.*iteration\_of\_week* **of Athena Query Federation **

   For example, the release number for **Release v2023\.8\.3 of Athena Query Federation** is 2023\.8\.3\.

## Finding and noting resource names<a name="connectors-updating-finding-and-noting-resource-names"></a>

In preparation for the upgrade, you must find and note the following information:

1. The Lambda function name for the connector\.

1. The Lambda function environment variables\.

1. The Lambda application name, which manages the Lambda function for the connector\.

**To find resource names from the Athena console**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Data sources**\.

1. In the **Data source name** column, choose the link to the data source for your connector\.

1. In the **Data source details** section, under **Lambda function**, choose the link to your Lambda function\.  
![\[Choose the link to your Lambda function.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connectors-updating-1.png)

1. On the **Functions** page, in the **Function name** column, note the function name for your connector\.  
![\[Note the function name.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connectors-updating-2.png)

1. Choose the function name link\.

1. Under the **Function overview** section, choose the **Configuration** tab\.

1. In the pane on the left, choose **Environment variables**\.

1. In the **Environment variables** section, make a note of the keys and their corresponding values\.

1. Scroll to the top of the page\.

1. In the message **This function belongs to an application\. Click here to manage it**, choose the **Click here** link\.

1. On the **serverlessrepo\-*your\_application\_name*** page, make a note of your application name without **serverlessrepo**\. For example, if the application name is **serverlessrepo\-DynamoDbTestApp**, then your application name is **DynamoDbTestApp**\.

1. Stay on the Lambda console page for your application, and then continue with the steps in **Finding the version of the connector that you are using**\.

## Finding the version of the connector that you are using<a name="connectors-updating-finding-the-version-that-you-are-using"></a>

Follow these steps to find the version of the connector that you are using\.

**To find the version of the connector that you are using**

1. On the Lambda console page for your Lambda application, choose the **Deployments** tab\.

1. On the **Deployments** tab, expand **SAM template**\.

1. Search for **CodeUri**\.

1. In the **Key** field under **CodeUri**, find the following string:

   ```
   applications-connector_name-versions-year.week_of_year.iteration_of_week/hash_number
   ```

   The following example shows a string for the CloudWatch connector:

   ```
   applications-AthenaCloudwatchConnector-versions-2021.42.1/15151159...
   ```

1. Record the value for *year*\.*week\_of\_year*\.*iteration\_of\_week* \(for example, **2021\.42\.1**\)\. This is the version for your connector\.

## Deploying the new version of your connector<a name="connectors-updating-deploying-the-new-version"></a>

Follow these steps to deploy a new version of your connector\.

**To deploy a new version of your connector**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Data sources**\.

1. On the **Data sources** page, choose **Create data source**\.

1. Choose the data source that you want to upgrade, and then choose **Next**\.

1. In the **Connection details** section, choose **Create Lambda function**\. This opens the Lambda console where you will be able to deploy your updated application\.  
![\[Connector page in the AWS Lambda console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connectors-updating-3.png)

1. Because you are not actually creating a new data source, you can close the Athena console tab\.

1. On the Lambda console page for the connector, perform the following steps:

   1. Ensure that you have removed the **serverlessrepo\-** prefix from your application name, and then copy the application name to the **Application name** field\.

   1. Copy your Lambda function name to the **AthenaCatalogName** field\. Some connectors call this field **LambdaFunctionName**\.

   1. Copy the environment variables that you recorded into their corresponding fields\.

1. Select the option **I acknowledge that this app creates custom IAM roles and resource policies**, and then choose **Deploy**\.

1. To verify that your application has been updated, choose the **Deployments** tab\.

   The **Deployment history** section shows that your update is complete\.  
![\[Connector update completed.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connectors-updating-4.png)

1. To confirm the new version number, you can expand **SAM template** as before, find **CodeUri**, and check the connector version number in the **Key** field\.

You can now use your updated connector to create Athena federated queries\.