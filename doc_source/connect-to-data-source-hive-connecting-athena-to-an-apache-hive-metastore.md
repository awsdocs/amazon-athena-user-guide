# Connecting Athena to an Apache Hive Metastore<a name="connect-to-data-source-hive-connecting-athena-to-an-apache-hive-metastore"></a>

To connect Athena to an Apache Hive metastore, you must create and configure a Lambda function\. For a basic implementation, you can perform all required steps from the Athena management console\.

**To connect Athena to a Hive metastore**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Connect data source**\.  
![\[Choose Connect data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-1.png)

1. On the **Connect data source** page, for **Choose a metadata catalog**, choose **Apache Hive metastore**\.  
![\[Choose Apache Hive metastore.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-2.png)

1. Choose **Next**\.

1. On the **Connection details** page, for **Lambda function**, choose **Create Lambda function**\.  
![\[Choose Create Lambda function.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-3.png)

1. In the **Lambda function** dialog box, enter the information for the Lambda function\. To use the default implementation, accept the defaults for the function code location in Amazon S3 and the Lambda handler\.  
![\[Enter the information in the Lambda function dialog box.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-4.png)
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

1. Click **Create**\. The **Connection details** page informs you that the function is being created\. When the operation completes, your function name is available in the **Choose a function name** box, and your Lambda function ARN is displayed\.  
![\[Lambda function created.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-5.png)

1. For **Catalog name**, enter a unique name to use for the data source in your SQL queries\. The name can be up to 127 characters long and must be unique within your account\. It cannot be changed after creation\. Valid characters are a\-z, A\-z, 0\-9, \_\(underscore\), @\(ampersand\), and \-\(hyphen\)\. The names `awsdatacatalog`, `hive`, `jmx`, and `system` are reserved by Athena and cannot be used for custom catalog names\.

1. Click **Connect** to connect Athena to your data source\.

   The **Data sources** page shows a list of your connected catalogs\. All registered catalogs are visible to all users in the same AWS account\.   
![\[The Data sources page shows your connected catalogs.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-to-data-source-hive-6.png)

1. You can now use the **Catalog name** that you specified to reference the Hive metastore in your SQL queries\. In your SQL queries, use the following example syntax, replacing `hms-catalog-1` with the catalog name that you specified earlier\.

   ```
   SELECT * FROM hms-catalog-1.CustomerData.customers; 
   ```

1. To view, edit, or delete the data sources that you create, see [Managing Data Sources](data-sources-managing.md)\.