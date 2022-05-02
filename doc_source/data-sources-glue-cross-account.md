# Registering an AWS Glue Data Catalog from another account<a name="data-sources-glue-cross-account"></a>

You can use Athena's cross\-account AWS Glue catalog feature to register an AWS Glue catalog from an account other than your own\. After you configure the required IAM permissions for AWS Glue and register the catalog as an Athena `DataCatalog` resource, you can use Athena to run cross\-account queries\. For information about configuring the required permissions, see [Cross\-account access to AWS Glue data catalogs](security-iam-cross-account-glue-catalog-access.md)\.

The following procedure shows you how to use the Athena console to configure an AWS Glue Data Catalog in an Amazon Web Services account other than your own as a data source\.

**To register an AWS Glue Data Catalog from another account**

1. Follow the steps in [Cross\-account access to AWS Glue data catalogs](security-iam-cross-account-glue-catalog-access.md) to ensure that you have permissions to query the data catalog in the other account\.

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. Choose **Data sources**\.

1. On the upper right, choose **Connect data source**\.

1. In the **AWS Glue Data Catalog** section, for **Choose an AWS Glue Data Catalog**, choose **AWS Glue Data Catalog in another account**\.

1. For **Data source details**, enter the following information:
   + **Data source name** – Enter the name that you want to use in your SQL queries to refer to the data catalog in the other account\.
   + **Description** – \(Optional\) Enter a description of the data catalog in the other account\.
   + **Catalog ID** – Enter the 12\-digit Amazon Web Services account ID of the account to which the data catalog belongs\. The Amazon Web Services account ID is the catalog ID\.

1. \(Optional\) For **Tags**, enter key\-value pairs that you want to associate with the data source\. For more information about tags, see [Tagging Athena resources](tags.md)\.

1. Choose **Register**\. On the **Data sources** page, the data catalog that you entered is listed in the **Catalog name** column\.

1. To view or edit information about the new data catalog, choose the catalog, and then choose **Edit**\.

1. To delete the new data catalog, choose the catalog, and then choose **Delete**\.