# Amazon Athena Teradata connector<a name="athena-prebuilt-data-connectors-teradata"></a>

The Amazon Athena connector for [Teradata](https://www.teradata.com/) enables Amazon Athena to run SQL queries on data stored in Teradata\.

Before you use this connector, you must set up a VPC and a security group\. For more information, see [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)\.

## Lambda layer prerequisite<a name="athena-prebuilt-data-connectors-teradata-layer-steps"></a>

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

For information about deploying Athena data source connectors, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md)\.

For information about configuration options, data type support, and other considerations, see [Amazon Athena Teradata connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-teradata/) on [GitHub](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors)\.