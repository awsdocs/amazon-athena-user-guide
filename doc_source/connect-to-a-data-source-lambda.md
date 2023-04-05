# Deploying a data source connector<a name="connect-to-a-data-source-lambda"></a>

Preparing to create federated queries is a two\-part process: deploying a Lambda function data source connector, and connecting the Lambda function to a data source\. In this process, you give the Lambda function a name that you can later choose in the Athena console and give the connector a name that you can reference in your SQL queries\.

**Note**  
To use the Athena Federated Query feature with AWS Secrets Manager, you must configure an Amazon VPC private endpoint for Secrets Manager\. For more information, see [Create a Secrets Manager VPC private endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint-create) in the *AWS Secrets Manager User Guide*\.

**Topics**
+ [Using the Athena console](connect-to-a-data-source-lambda-deploying.md)
+ [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)
+ [Creating a VPC for a data source connector](athena-connectors-vpc-creation.md)
+ [Enabling cross\-account federated queries](xacct-fed-query-enable.md)
+ [Updating a data source connector](connectors-updating.md)