# Using the Amazon Athena Power BI connector<a name="connect-with-odbc-and-power-bi"></a>

On Windows operating systems, you can use the Microsoft Power BI connector for Amazon Athena to analyze data from Amazon Athena in Microsoft Power BI Desktop\. For information about Power BI, see [Microsoft power BI](https://powerbi.microsoft.com/)\. After you publish content to the Power BI service, you can use the July 2021 or later release of [Power BI gateway](https://powerbi.microsoft.com/gateway/) to keep the content up to date through on\-demand or scheduled refreshes\.

## Prerequisites<a name="connect-with-odbc-and-power-bi-prerequisites"></a>

Before you begin, make sure that your environment meets the following requirements\. The Amazon Athena ODBC driver is required\.
+ [AWS account](https://aws.amazon.com/)
+ [Permissions to use Athena](policy-actions.md)
+ [Amazon Athena ODBC driver](connect-with-odbc.md)
+ [Power BI desktop](https://powerbi.microsoft.com/en-us/desktop/)

## Capabilities supported<a name="connect-with-odbc-and-power-bi-capabilities-supported"></a>
+ **Import** – Selected tables and columns are imported into Power BI Desktop for querying\.
+ **DirectQuery** – No data is imported or copied into Power BI Desktop\. Power BI Desktop queries the underlying data source directly\.
+ **Power BI gateway** – An on\-premises data gateway in your AWS account that works like a bridge between the Microsoft Power BI Service and Athena\. The gateway is required to see your data on the Microsoft Power BI Service\.

## Connect to Amazon Athena<a name="connect-with-odbc-and-power-bi-connect-to-amazon-athena"></a>

To connect Power BI desktop to your Amazon Athena data, perform the following steps\.

**To connect to Athena data from power BI desktop**

1. Launch Power BI Desktop\.

1. Do one of the following:
   + Choose **File**, **Get Data**
   + From the **Home** ribbon, choose **Get Data**\.

1. In the search box, enter **Athena**\.

1. Select **Amazon Athena**, and then choose **Connect**\.  
![\[Choose the Amazon Athena connector\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-with-odbc-and-power-bi-1.png)

1. On the **Amazon Athena** connection page, enter the following information\.
   + For **DSN**, enter the name of the ODBC DSN that you want to use\. For instructions on configuring your DSN, see the [ODBC driver documentation](connect-with-odbc.md#connect-with-odbc-driver-documentation)\.
   + For **Data Connectivity mode**, choose a mode that is appropriate for your use case, following these general guidelines:
     + For smaller datasets, choose **Import**\. When using Import mode, Power BI works with Athena to import the contents of the entire dataset for use in your visualizations\.
     + For larger datasets, choose **DirectQuery**\. In DirectQuery mode, no data is downloaded to your workstation\. While you create or interact with a visualization, Microsoft Power BI works with Athena to dynamically query the underlying data source so that you're always viewing current data\. For more information about DirectQuery, see [Use DirectQuery in power BI desktop](https://docs.microsoft.com/power-bi/connect-data/desktop-use-directquery) in the Microsoft documentation\.  
![\[Enter your data connectivity information\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-with-odbc-and-power-bi-2.png)

1. Choose **OK**\.

1. At the prompt to configure data source authentication, choose either **Use Data Source Configuration** or **AAD Authentication**, and then choose **Connect**\.  
![\[Choose a data source authentication method\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-with-odbc-and-power-bi-3.png)

   Your data catalog, databases, and tables appear in the **Navigator** dialog box\.  
![\[The Navigator displays your data\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-with-odbc-and-power-bi-4.png)

1. In the **Display Options** pane, select the check box for the dataset that you want to use\.

1. If you want to transform the dataset before you import it, go to the bottom of the dialog box and choose **Transform Data**\. This opens the Power Query Editor so that you can filter and refine the set of data you want to use\.

1. Choose **Load**\. After the load is complete, you can create visualizations like the one in the following image\. If you selected **DirectQuery** as the import mode, Power BI issues a query to Athena for the visualization that you requested\.  
![\[A sample data visualization\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-with-odbc-and-power-bi-5.png)

## Setting up an on\-premises gateway<a name="connect-with-odbc-and-power-bi-gateway-setup"></a>

You can publish dashboards and datasets to the Power BI service so that other users can interact with them through web, mobile, and embedded apps\. To see your data in the Microsoft Power BI Service, you install the Microsoft Power BI on\-premises data gateway in your AWS account\. The gateway works like a bridge between the Microsoft Power BI Service and Athena\.

**To download, install, and test an on\-premises data gateway**

1. Visit the [Microsoft power BI gateway download](https://powerbi.microsoft.com/en-us/gateway/) page and choose either personal mode or standard mode\. Personal mode is useful for testing the Athena connector locally\. Standard mode is appropriate in a multiuser production setting\.

1. To install an on\-premises gateway \(either personal or standard mode\), see [Install an on\-premises data gateway](https://docs.microsoft.com/en-us/data-integration/gateway/service-gateway-install) in the Microsoft documentation\.

1. To test the gateway, follow the steps in [Use custom data connectors with the on\-premises data gateway](https://docs.microsoft.com/en-us/power-bi/connect-data/service-gateway-custom-connectors) in the Microsoft documentation\.

For more information about on\-premises data gateways, see the following Microsoft resources\.
+ [What is an on\-premises data gateway?](https://docs.microsoft.com/en-us/power-bi/connect-data/service-gateway-onprem)
+ [Guidance for deploying a data gateway for power BI](https://docs.microsoft.com/en-us/power-bi/connect-data/service-gateway-deployment-guidance)

For an example of configuring Power BI Gateway for use with Athena, see the AWS Big Data Blog article [Creating dashboards quickly on Microsoft power BI using amazon Athena](http://aws.amazon.com/blogs/big-data/creating-dashboards-quickly-on-microsoft-power-bi-using-amazon-athena/)\.