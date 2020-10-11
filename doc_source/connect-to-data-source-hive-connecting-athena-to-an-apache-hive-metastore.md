# Connecting Athena to an Apache Hive Metastore<a name="connect-to-data-source-hive-connecting-athena-to-an-apache-hive-metastore"></a>

To connect Athena to an Apache Hive metastore, you must create and configure a Lambda function\. For a basic implementation, you can perform all required steps starting from the Athena management console\.

**To connect Athena to a Hive metastore**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Connect data source**\.  
![\[Choose Connect data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-1.png)

1. On the **Connect data source** page, for **Choose a metadata catalog**, choose **Apache Hive metastore**\.  
![\[Choose Apache Hive metastore.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-2.png)

1. Choose **Next**\.

1. On the **Connection details** page, for **Lambda function**, choose **Configure new AWS Lambda function**\.  
![\[Choose Configure new AWS Lambda function.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-3.png)

   The **AthenaHiveMetastoreFunction** page opens in the AWS Lambda console\.  
![\[The AthenaHiveMetastoreFunction page in the AWS Lambda console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-4.png)

1. Under **Application settings**, enter the parameters for your Lambda function\.
   + **LambdaFuncName** – Provide a name for the function\. For example, **myHiveMetastore**\.
   + **SpillLocation** – Specify an Amazon S3 location in this account to hold spillover metadata if the Lambda function reponse size exceeds 4MB\.
   + **HMSUris** – Enter the URI of your Hive metastore host that uses the Thrift protocol at port 9083\. Use the syntax `thrift://<host_name>:9083`\.
   + **LambdaMemory** – Specify a value from 128MB to 3008MB\. The Lambda function is allocated CPU cycles proportional to the amount of memory that you configure\. The default is 1024\.
   + **LambdaTimeout** – Specify the maximum permissible Lambda invocation run time in seconds from 1 to 900 \(900 seconds is 15 minutes\)\. The default is 300 seconds \(5 minutes\)\.
   + **VPCSecurityGroupIds** – Enter a comma\-separated list of VPC security group IDs for the Hive metastore\.
   + **VPCSubnetIds** – Enter a comma\-separated list of VPC subnet IDs for the Hive metastore\.

1. On the bottom right of the **Application details** page, select **I acknowledge that this app creates custom IAM roles**, and then choose **Deploy**\.  
![\[Deploying the Lambda function app from the AWS Lambda console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-4a.png)

   When the deployment completes, your function appears in your list of Lambda applications\. Now that the Hive metastore function has been deployed to your account, you can configure Athena to use it\.  
![\[A Lambda Hive metastore app in the AWS Lambda console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-4b.png)

1. Return to the **Connection details** page of the **Data Sources** tab in the Athena console\.

1. Choose the **Refresh** icon next to **Choose Lambda function**\. Refreshing the list of available functions causes your newly created function to appear in the list\.  
![\[Choose Refresh.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-4c.png)

1. Now that your Lambda function is available, choose it\.  
![\[Choose the Lambda function that you created.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-4d.png)

   A new **Lambda function ARN** entry shows the ARN of your Lambda function\.  
![\[Lambda function ARN.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-5.png)

1. For **Catalog name**, enter a unique name that you will use in your SQL queries to reference the data source\. The name can be up to 127 characters long and must be unique within your account\. It cannot be changed after creation\. Valid characters are a\-z, A\-z, 0\-9, \_\(underscore\), @\(ampersand\), and \-\(hyphen\)\. The names `awsdatacatalog`, `hive`, `jmx`, and `system` are reserved by Athena and cannot be used for custom catalog names\.

1. \(Optional\) For **Description**, enter text that describes your data catalog\.

1. Choose **Connect**\. This connects Athena to your Hive metastore catalog\.

   The **Data sources** page shows a list of your connected catalogs, including the catalog that you just connected\. All registered catalogs are visible to all users in the same AWS account\.   
![\[The Data sources page shows your connected catalogs.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-6.png)

1. You can now use the **Catalog name** that you specified to reference the Hive metastore in your SQL queries\. In your SQL queries, use the following example syntax, replacing `hms-catalog-1` with the catalog name that you specified earlier\.

   ```
   SELECT * FROM hms-catalog-1.CustomerData.customers; 
   ```

1. To view, edit, or delete the data sources that you create, see [Managing Data Sources](data-sources-managing.md)\.