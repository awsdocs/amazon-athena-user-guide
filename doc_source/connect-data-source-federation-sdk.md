# Writing a Data Source Connector Using the Athena Query Federation SDK<a name="connect-data-source-federation-sdk"></a>

To write your own [data source connectors](connect-to-a-data-source.md), you can use the [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-federation-sdk)\. The Athena Query Federation SDK defines a set of interfaces and wire protocols that you can use to enable Athena to delegate portions of its query execution plan to code that you write and deploy\. The SDK includes a connector suite and an example connector\.

You can also customize Amazon Athena's [prebuilt connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) for your own use\. You can modify a copy of the source code from GitHub and then use the [Connector Publish Tool](https://github.com/awslabs/aws-athena-query-federation/wiki/Connector_Publish_Tool) to create your own AWS Serverless Application Repository package\. After you deploy your connector in this way, you can use it in your Athena queries\.

**Note**  
To use this feature in preview, you must create an Athena workgroup named `AmazonAthenaPreviewFunctionality` and join that workgroup\. For more information, see [Managing Workgroups](workgroups-create-update-delete.md)\.

For information about how to download the SDK and detailed instructions for writing your own connector, see [Example Athena Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.