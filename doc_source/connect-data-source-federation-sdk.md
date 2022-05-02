# Writing a data source connector using the Athena Query Federation SDK<a name="connect-data-source-federation-sdk"></a>

To write your own [data source connectors](connect-to-a-data-source.md), you can use the [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-federation-sdk)\. The Athena Query Federation SDK defines a set of interfaces and wire protocols that you can use to enable Athena to delegate portions of its query execution plan to code that you write and deploy\. The SDK includes a connector suite and an example connector\.

You can also customize Amazon Athena's [prebuilt connectors](https://github.com/awslabs/aws-athena-query-federation/wiki/Available-Connectors) for your own use\. You can modify a copy of the source code from GitHub and then use the [Connector publish tool](https://github.com/awslabs/aws-athena-query-federation/wiki/Connector_Publish_Tool) to create your own AWS Serverless Application Repository package\. After you deploy your connector in this way, you can use it in your Athena queries\.

**Note**  
To use the Amazon Athena Federated Query feature, set your workgroup to Athena engine version 2\. For steps, see [Changing Athena engine versions](engine-versions-changing.md)\.

For information about how to download the SDK and detailed instructions for writing your own connector, see [Example Athena connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-example) on GitHub\.