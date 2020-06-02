# Managing Data Sources<a name="data-sources-managing"></a>

You can use the **Data Sources** page of the Athena console to view, edit, or delete the data sources that you create, including Athena data source connector, AWS Glue Data Catalog, and Hive metastore catalog types\.

**To view a data source**
+ Do one of the following:
  + Choose the catalog name of the data source\.
  + Select the button next to the catalog name, and then choose **View details**\. 

  The details page includes options to **Edit** or **Delete** the data source\.

**To edit a data source**

1. Do one of the following:
   + Select the button next to the catalog name, and then choose **Edit**\. 
   + Choose the catalog name of the data source, and then choose **Edit**\.  
![\[Choose the catalog name.\]](http://docs.aws.amazon.com/athena/latest/ug/images/data-sources-managing-choose-catalog-name.png)  
![\[Choose Edit to edit a data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/data-sources-managing-edit.png)

1. On the **Edit** page for the metastore, you can choose a different Lambda function for the data source or change the description of the existing function\. When you edit an AWS Glue catalog, the AWS Glue console opens the corresponding catalog for editing\.  
![\[Editing a data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/data-sources-managing-editing.png)

1. Choose **Save**\.

**To delete a data source**

1. Select the button next to the data source or the name of the data source, and then choose **Delete**\. You are warned that when you delete a metastore data source, its corresponding Data Catalog, tables, and views are removed from the query editor\. Saved queries that used the metastore no longer run in Athena\.

1. Choose **Delete**\.