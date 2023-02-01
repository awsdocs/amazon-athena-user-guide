# Running CTAS queries in the console<a name="ctas-console"></a>

In the Athena console, you can create a CTAS query from another query\.<a name="ctas-create-from-query"></a>

**To create a CTAS query from another query**

1. Run the query in the Athena console query editor\.

1. At the bottom of the query editor, choose the **Create** option, and then choose **Table from query**\.

1. In the **Create table as select** form, complete the fields as follows:

   1. For **Table name**, enter the name for your new table\. Use only lowercase and underscores, such as `my_select_query_parquet`\.

   1. For **Database configuration**, use the options to choose an existing database or create a database\.

   1. \(Optional\) In **Result configuration**, for **Location of CTAS query results**, if your workgroup query results location setting does not override this option, do one of the following:
      + Enter the path to an existing S3 location in the search box, or choose **Browse S3** to choose a location from a list\.
      + Choose **View** to open the **Buckets** page of the Amazon S3 console where you can view more information about your existing buckets and choose or create a bucket with your own settings\.

      You should specify an empty location in Amazon S3 where the data will be output\. If data already exists in the location that you specify, the query fails with an error\. 

      If your workgroup query results location setting overrides this location setting, Athena creates your table in the location `s3://workgroup_query_results_location/tables/query_id/`

   1. For **Data format**, specify the format that your data is in\.
      + **Table type** – The default table type in Athena is Apache Hive\. 
      + **File format** – Choose among options like CSV, TSV, JSON, Parquet, or ORC\. For information about the Parquet and ORC formats, see [Columnar storage formats](columnar-storage.md)\.
      + **Write compression** – \(Optional\) Choose a compression format\. Athena supports a variety of compression formats for reading and writing data, including reading from a table that uses multiple compression formats\. For example, Athena can successfully read the data in a table that uses Parquet file format when some Parquet files are compressed with Snappy and other Parquet files are compressed with GZIP\. The same principle applies for ORC, text file, and JSON storage formats\. For more information, see [Athena compression support](compression-formats.md)\.
      + **Partitions** – \(Optional\) Select the columns that you want to partition\. Partitioning your data restricts the amount of data scanned by each query, thus improving performance and reducing cost\. You can partition your data by any key\. For more information, see [Partitioning data in Athena](partitions.md)\.
      + **Buckets** – \(Optional\) Select the columns that you want to bucket\. Bucketing is a technique that groups data based on specific columns together within a single partition\. These columns are known as *bucket keys*\. By grouping related data into a single bucket \(a file within a partition\), you significantly reduce the amount of data scanned by Athena, thus improving query performance and reducing cost\. For more information, see [Bucketing vs partitioning](ctas-bucketing-vs-partitioning.md)\.

   1. For **Preview table query**, review your query\. For query syntax, see [ CREATE TABLE AS ](create-table-as.md)\.

   1. Choose **Create table**\.<a name="ctas-create-new"></a>

**To create a CTAS query using a SQL template**

Use the `CREATE TABLE AS SELECT` template to create a CTAS query in the query editor\.

1. In the Athena console, next to **Tables and views**, choose **Create table**, and then choose **CREATE TABLE AS SELECT**\. This populates the query editor with a CTAS query with placeholder values\.

1. In the query editor, edit the query as needed\. For query syntax, see [ CREATE TABLE AS ](create-table-as.md)\.

1. Choose **Run**\.

For examples, see [Examples of CTAS queries](ctas-examples.md)\.

