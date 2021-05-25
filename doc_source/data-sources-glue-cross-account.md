# Registering an AWS Glue Data Catalog from Another Account<a name="data-sources-glue-cross-account"></a>

You can use Athena's cross\-account AWS Glue catalog feature to register an AWS Glue catalog from an account other than your own\. After you configure the required IAM permissions for AWS Glue and register the catalog as an Athena `DataCatalog` resource, you can use Athena to run cross\-account queries\. For information about configuring the required permissions, see [Cross\-Account Access to Data Catalogs](security-iam-cross-account-glue-catalog-access.md)\.

The following procedure shows you how to use the Athena console to configure an AWS Glue Data Catalog in an Amazon Web Services account other than your own as a data source\.

**To register an AWS Glue Data Catalog from another account**

1. Follow the steps in [Cross\-Account Access to Data Catalogs](security-iam-cross-account-glue-catalog-access.md) to ensure that you have permissions to query the data catalog in the other account\.

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Data sources**\.

1. Choose **Connect data source**\.

1. Choose **Next**\.

1. Choose **AWS Glue Data Catalog in another account**\.

1. Choose **Next**\.

1. On the **Connection details: AWS AWS Glue Data Catalog in another account** page, enter the following information:
   + **Catalog name** – Enter the name that you want to use in your SQL queries to refer to the data catalog in the other account\.
   + **Description** – \(Optional\) Enter a description of the data catalog in the other account\.
   + **Catalog ID** – Enter the 12\-digit Amazon Web Services account ID of the account to which the data catalog belongs\. The Amazon Web Services account ID is the catalog ID\.

1. Choose **Register**\. On the **Data sources** page, the data catalog that you entered is listed in the **Catalog name** column\.

1. To view or edit information about the new data catalog, choose the catalog, and then choose **Edit**\.

1. To delete the new data catalog, choose the catalog, and then choose **Delete**\.