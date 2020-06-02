# Using the AWS Serverless Application Repository to Deploy a Hive Data Source Connector<a name="connect-data-source-sar-hive"></a>

You can also use the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/) to deploy an Athena data source connector for Hive\. Choose the connector that you want to use, provide the parameters that the connector requires, and then deploy the connector to your account\.

**To use the AWS Serverless Application Repository to deploy a data source connector for Hive to your account**

1. Sign in to the AWS Management Console and open the **Serverless App Repository**\.

1. In the navigation pane, choose **Available applications**\.

1. Select the option **Show apps that create custom IAM roles or resource policies**\.

1. In the search box, type the name of one of the following connectors\. The two applications have the same functionality and differ only in their implementation\. You can use either one to create a Lambda function that connects Athena to your Hive metastore\.
   + **AthenaHiveMetastoreFunction** – Uber Lambda function `.jar` file\.
   + **AthenaHiveMetastoreFunctionWithLayer** – Lambda layer and thin Lambda function `.jar` file\.

1. Choose the name of the connector\.   
![\[Choose the name of the Athena data source connector for Hive.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-sar-hive-1.png)

1. On the Lambda function's **Application details** page, configure the parameters for the function, including the following\. 
   + **Lambda function name** – Provide a name for the function\. For example, **myHiveMetastore**\.
   + **Lambda execution role** – Choose an IAM role or click **Create a new role** to create one\.
   + **Function code** – The location in Amazon S3 for the Lambda function JAR file\. Use the default or enter the location of your custom JAR file\.
   + **Lambda handler** – The method in the JAR file that implements the Hive connector\. Use the default or replace it with the handler in your custom code\.
   + **Hive metastore \(HMS\) URI** – Enter the name of your Hive metastore host that uses the Thrift protocol at port 9083 with the syntax `thrift://<host_name>:9083`\.
   + **Spill location in S3** – Specify an Amazon S3 location in this account to hold spillover metadata if the Lambda function reponse size exceeds 4MB\.
   + **Virtual Private Cloud \(VPC\)** – Choose the VPC that contains your Hive metastore\.
   + **Subnets** – Choose the VPC subnets for Lambda to use to set up your VPC configuration\.
   + **Security Groups** – Choose the VPC security groups for Lambda to use to set up your VPC configuration\.
   + **Memory** – Specify a value from 128MB to 3008MB\. The Lambda function is allocated CPU cycles proportional to the amount of memory that you configure\.
   + **Timeout** – Specify a value from 1 second to 15 minutes 59 seconds\. The default is 3 seconds\.

1. At the bottom right of the **Application details** page, choose **Deploy\.** When the deployment completes, the function appears in the Lambda console\.