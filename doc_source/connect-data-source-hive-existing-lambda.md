# Configure Athena to Use a Deployed Hive Metastore Connector<a name="connect-data-source-hive-existing-lambda"></a>

After you have deployed a Lambda data source connector like `AthenaHiveMetastoreFunction` to your account, you can configure Athena to use it\. To do so, you create a data source name that refers to your external Hive metastore to use in your Athena queries\.

**To connect Athena to your Hive metastore using an existing Lambda function**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. Choose **Data sources**\.

1. On the **Data sources** page, choose **Connect data source**\.

1. On the **Connect data sources** page, for **Data source selection**, choose **S3 \- Apache Hive metastore**\.

1. In the **Data source details** section, for **Data source name**, enter the name that you want to use in your SQL statements when you query the data source from Athena \(for example, `MyHiveMetastore`\)\. The name can be up to 127 characters and must be unique within your account\. It cannot be changed after you create it\. Valid characters are a\-z, A\-Z, 0\-9, \_ \(underscore\), @ \(at sign\) and \- \(hyphen\)\. The names `awsdatacatalog`, `hive`, `jmx`, and `system` are reserved by Athena and cannot be used for data source names\. 

1. In the **Lambda function** section, make sure that **Use an existing Lambda function** is selected\.

1. Choose the refresh icon next to the **Choose or enter a Lambda function** box\.

1. Choose the function that you deployed from the Lambda console\. The ARN of the Lambda function displays\.

1. \(Optional\) For **Tags**, add key\-value pairs to associate with this data source\. For more information about tags, see [Tagging Athena Resources](tags.md)\.

1. Choose **Connect data source**\. The **Data sources** page shows your Hive metastore in the list of data sources\.

1. You can now use the **Data source name** that you specified to reference the Hive metastore in your SQL queries\.

   In your SQL queries, use the following example syntax, replacing `ehms-catalog` with the data source name that you specified earlier\.

   ```
   SELECT * FROM ehms-catalog.CustomerData.customers 
   ```

1. To view, edit, or delete the data sources that you create, see [Managing Data Sources](data-sources-managing.md)\.