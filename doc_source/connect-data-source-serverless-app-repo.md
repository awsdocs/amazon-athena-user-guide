# Using the AWS Serverless Application Repository to Deploy a Data Source Connector<a name="connect-data-source-serverless-app-repo"></a>

You can also use the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/) to deploy an Athena data source connector\. You find the connector that you want to use, provide the parameters that the connector requires, and then deploy the connector to your account\.

**Note**  
To use this feature in preview, you must create an Athena workgroup named `AmazonAthenaPreviewFunctionality` and join that workgroup\. For more information, see [Managing Workgroups](workgroups-create-update-delete.md)\.

**To use the AWS Serverless Application Repository to deploy a data source connector to your account**

1. Sign in to the AWS Management Console and open the **Serverless App Repository**\.

1. In the navigation pane, choose **Available applications**\.

1. Select the option **Show apps that create custom IAM roles or resource policies**\.

1. In the search box, type the name of the connector, or search for applications published with the author name **Amazon Athena Federation**\. This author name is reserved for applications that the Amazon Athena team has written, tested, and validated\. For a list of prebuilt Athena data connectors, see [Using Athena Data Source Connectors](athena-prebuilt-data-connectors.md)\.

1. Choose the name of the connector\. This opens the Lambda function's **Application details** page in the AWS Lambda console\.

1. On the right side of the details page, for **Application settings**, **SpillBucket**, specify an Amazon S3 bucket to receive data from large response payloads\. For information about the remaining configurable options, see the corresponding [Available Connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) topic on GitHub\.

1. At the bottom right of the **Application details** page, choose **Deploy\.**