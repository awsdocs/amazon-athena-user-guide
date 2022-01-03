# Managing Data Sources<a name="data-sources-managing"></a>

You can use the **Data Sources** page of the Athena console to manage the data sources that you create\.

**To view a data source**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Data sources**\.

1. From the list of data sources, choose the name of the data source that you want to view\.

**To edit a data source**

1. On the **Data sources** page, do one of the following:
   + Select the button next to the catalog name, and then choose **Actions**, **Edit**\. 
   + Choose the name of the data source\. Then on the details page, choose **Actions**, **Edit**\.

1. On the **Edit** page, you can choose a different Lambda function for the data source, change the description, or add custom tags\. For more information about tags, see [Tagging Athena Resources](tags.md)\.

1. Choose **Save**\.

1. To edit your **AwsDataCatalog** data source, choose the **AwsDataCatalog** link to open its details page\. Then, on the details page, choose the link to the AWS Glue console where you can edit your catalog\.

**To share a data source**  
For information about sharing data sources, visit the following links\.
+ For non\-Hive Lambda\-based data sources, see [Enabling Cross\-Account Federated Queries](xacct-fed-query-enable.md)\.
+ For AWS Glue Data Catalogs, see [Cross\-Account Access to AWS Glue Data Catalogs](security-iam-cross-account-glue-catalog-access.md)\.

**To delete a data source**

1. On the **Data sources** page, do one of the following:
   + Select the button next to the catalog name, and then choose **Actions**, **Delete**\. 
   + Choose the name of the data source, and then, on the details page, choose **Actions**, **Delete**\.
**Note**  
The **AwsDataCatalog** is the default data source in your account and cannot be deleted\.

   You are warned that when you delete a data source, its corresponding data catalog, tables, and views are removed from the query editor\. Saved queries that used the data source will no longer run in Athena\.

1. To confirm the deletion, type the name of the data source, and then choose **Delete**\.