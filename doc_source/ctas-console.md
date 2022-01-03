# Running CTAS Queries in the Console<a name="ctas-console"></a>

In the Athena console, you can: 
+ [Create a CTAS query from another query](#ctas-create-from-query)
+ [Create a CTAS query using a template](#ctas-create-from-query)<a name="ctas-create-from-query"></a>

**To create a CTAS query from another query**

1. Run the query in the Athena console query editor\.

1. At the bottom of the query editor, choose the **Create** option, and then choose **Table from query**\.

1. In the **Create Table From S3 bucket data** form, complete the fields as follows:

   1. For **Table name**, specify the name for your new table\. Use only lowercase and underscores, such as `my_select_query_parquet`\.

   1. For **Description**, optionally add a comment to describe your table\.

   1. For **Database**, use the options to choose an existing database or create a database\.

   1. For **Dataset**, specify an empty location in Amazon S3 where the data will be output\. If data already exists in the location that you specify, the query fails with an error\.

   1. For **Data format**, select from the list of supported formats\. For information about the Parquet and ORC formats, see [Columnar Storage Formats](columnar-storage.md)\.

   1. For **Preview table query**, review your query and revise it as needed\. For query syntax, see [CREATE TABLE AS](create-table-as.md)\.

   1. Choose **Create table**\.<a name="ctas-create-new"></a>

**To create a CTAS query using a template**

Use the `CREATE TABLE AS SELECT` template to create a CTAS query in the query editor\.

1. In the Athena console, next to **Tables and views**, choose **Create table**, and then choose **CREATE TABLE AS SELECT**\. This populates the query editor with a CTAS query with placeholder values\.

1. In the query editor, edit the query as needed\. For query syntax, see [CREATE TABLE AS](create-table-as.md)\.

1. Choose **Run**\.

For examples, see [Examples of CTAS Queries](ctas-examples.md)\.

