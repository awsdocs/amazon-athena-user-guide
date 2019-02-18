# Running CTAS Queries in the Console<a name="ctas-console"></a>

In the Athena console, you can: 
+ [Create a CTAS query from another query](#ctas-create-from-query)
+ [Create a CTAS query from scratch](#ctas-create-from-query)<a name="ctas-create-from-query"></a>

**To create a CTAS query from another query**

1. Run the query, choose **Create**, and then choose **Create table from query**\.  
![\[The screenshot for creating a table from query results.\]](http://docs.aws.amazon.com/athena/latest/ug/images/ctas-create-table-from-query.PNG)

1. In the **Create a new table on the results of a query** form, complete the fields as follows:

   1. For **Database**, select the database in which your query ran\.

   1. For **Table name**, specify the name for your new table\. Use only lowercase and underscores, such as `my_select_query_parquet`\.

   1. For **Description**, optionally add a comment to describe your query\.

   1. For **Output location**, optionally specify the location in Amazon S3, such as `s3://my_athena_results/mybucket/`\. If you don't specify a location and your workgroup does not [Override Client\-Side Settings](workgroups-settings-override.md), the following predefined location is used: `s3://aws-athena-query-results-<account>-<region>/<query-name-or-unsaved>/year/month/date/<query-id>/`\.

   1. For **Output data format**, select from the list of supported formats\. Parquet is used if you don't specify a format\. See [Columnar Storage Formats](columnar-storage.md)\.  
![\[The screenshot for the CTAS wizard screen.\]](http://docs.aws.amazon.com/athena/latest/ug/images/ctas-wizard.png)

   1. Choose **Next** to review your query and revise it as needed\. For query syntax, see [CREATE TABLE AS](create-table-as.md)\. The preview window opens, as shown in the following example:  
![\[The screenshot for the CTAS wizard preview screen.\]](http://docs.aws.amazon.com/athena/latest/ug/images/ctas_wizard_preview.png)

   1. Choose **Create**\.

1. Choose **Run query**\.<a name="ctas-create-new"></a>

**To create a CTAS query from scratch**

Use the `CREATE TABLE AS SELECT` template to create a CTAS query from scratch\.

1. In the Athena console, choose **Create table**, and then choose **CREATE TABLE AS SELECT**\.  
![\[The screenshot of the CREATE TABLE AS SELECT for a new table.\]](http://docs.aws.amazon.com/athena/latest/ug/images/ctas-create-new.PNG)

1. In the Query Editor, edit the query as needed, For query syntax, see [CREATE TABLE AS](create-table-as.md)\.

1. Choose **Run query**\.

1. Optionally, choose **Save as** to save the query\.

See also [Examples of CTAS Queries](ctas-examples.md)\.