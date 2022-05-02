# Using the AWS Serverless Application Repository to deploy a Hive data source connector<a name="connect-data-source-sar-hive"></a>

To deploy an Athena data source connector for Hive, you can use the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/) instead of starting with the Athena console\. Use the AWS Serverless Application Repository to find the connector that you want to use, provide the parameters that the connector requires, and then deploy the connector to your account\. Then, after you deploy the connector, you use the Athena console to make the data source available to Athena\.

**To use the AWS Serverless Application Repository to deploy a data source connector for Hive to your account**

1. Sign in to the AWS Management Console and open the **Serverless App Repository**\.

1. In the navigation pane, choose **Available applications**\.

1. Select the option **Show apps that create custom IAM roles or resource policies**\.

1. In the search box, enter **Hive**\. The connectors that appear include the following two:
   + **AthenaHiveMetastoreFunction** – Uber Lambda function `.jar` file\.
   + **AthenaHiveMetastoreFunctionWithLayer** – Lambda layer and thin Lambda function `.jar` file\.

    The two applications have the same functionality and differ only in their implementation\. You can use either one to create a Lambda function that connects Athena to your Hive metastore\.

1. Choose the name of the connector that you want to use\. This tutorial uses **AthenaHiveMetastoreFunction**\.  
![\[Choose the name of the Athena data source connector for Hive.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-sar-hive-1.png)

1. Under **Application settings**, enter the parameters for your Lambda function\.
   + **LambdaFuncName** – Provide a name for the function\. For example, **myHiveMetastore**\.
   + **SpillLocation** – Specify an Amazon S3 location in this account to hold spillover metadata if the Lambda function response size exceeds 4MB\.
   + **HMSUris** – Enter the URI of your Hive metastore host that uses the Thrift protocol at port 9083\. Use the syntax `thrift://<host_name>:9083`\.
   + **LambdaMemory** – Specify a value from 128MB to 3008MB\. The Lambda function is allocated CPU cycles proportional to the amount of memory that you configure\. The default is 1024\.
   + **LambdaTimeout** – Specify the maximum permissible Lambda invocation run time in seconds from 1 to 900 \(900 seconds is 15 minutes\)\. The default is 300 seconds \(5 minutes\)\.
   + **VPCSecurityGroupIds** – Enter a comma\-separated list of VPC security group IDs for the Hive metastore\.
   + **VPCSubnetIds** – Enter a comma\-separated list of VPC subnet IDs for the Hive metastore\.

1. On the bottom right of the **Application details** page, select **I acknowledge that this app creates custom IAM roles**, and then choose **Deploy**\.

At this point, you can configure Athena to use your Lambda function to connect to your Hive metastore\. For steps, see [Configure Athena to use a deployed Hive metastore connector](connect-data-source-hive-existing-lambda.md)\.