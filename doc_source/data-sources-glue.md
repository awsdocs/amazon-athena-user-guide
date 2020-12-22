# Using AWS Glue to Connect to Data Sources in Amazon S3<a name="data-sources-glue"></a>

Athena can connect to your data stored in Amazon S3 using the AWS Glue Data Catalog to store metadata such as table and column names\. After the connection is made, your databases, tables, and views appear in Athena's query editor\.

To define schema information for AWS Glue to use, you can set up an AWS Glue crawler to retrieve the information automatically, or you can manually add a table and enter the schema information\. 

## Setting up a Crawler<a name="data-sources-glue-crawler-setup"></a>

You set up a crawler by starting in the Athena console and then using the AWS Glue console in an integrated way\. When you create a crawler, you can choose data stores to crawl or point the crawler to existing catalog tables\.

**Note**  
The steps for setting up a crawler depend on the options available in the Athena console\. If the **Connect data source** link in **Option A** is not available, use the procedure in **Option B**\.

### Option A<a name="data-sources-glue-crawler-setup-option-a"></a>

**Option A: To set up a crawler in AWS Glue using the *Connect data source* link**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Connect data source**\. If the **Connect data source** link is not present, use **Option B**\.  
![\[Choose Connect data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/data-sources-glue-connect-data-source.png)

1. On the **Connect data source** page, choose **AWS Glue Data Catalog**\.

1. Click **Next**\.

1. On the **Connection details** page, choose **Set up crawler in AWS Glue to retrieve schema information automatically**\.

1. Click **Connect to AWS AWS Glue**\.

1. On the **AWS Glue** console **Add crawler** page, follow the steps to create a crawler\. 

   For more information, see [Populating the AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html)\.

### Option B<a name="data-sources-glue-crawler-setup-option-b"></a>

Use the following procedure to set up a AWS Glue crawler if the **Connect data source** link in **Option A** is not available in the Athena console\.<a name="data-sources-glue-catalog-link-procedure"></a>

**Option B: To set up a crawler in AWS Glue from the *AWS Glue Data Catalog* link**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **AWS Glue Data Catalog**\.  
![\[Choose AWS Glue Data Catalog.\]](http://docs.aws.amazon.com/athena/latest/ug/images/data-sources-glue-crawler-option-b-header.png)

1. On the AWS Glue console **Tables** page, choose **Add tables using a crawler**\.  
![\[Choose Add tables using a crawler.\]](http://docs.aws.amazon.com/athena/latest/ug/images/data-sources-glue-crawler-option-b-add-tables.png)

1. On the **AWS Glue** console **Add crawler** page, follow the steps to create a crawler\. 

   For more information, see [Populating the AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html)\.

**Note**  
Athena does not recognize [exclude patterns](https://docs.aws.amazon.com/glue/latest/dg/define-crawler.html#crawler-data-stores-exclude) that you specify for an AWS Glue crawler\. For example, if you have an Amazon S3 bucket that contains both `.csv` and `.json` files and you exclude the `.json` files from the crawler, Athena queries both groups of files\. To avoid this, place the files that you want to exclude in a different location\.

## Adding a Schema Table Manually<a name="data-sources-glue-manual-table"></a>

The following procedure shows you how to use the Athena console to add a table manually\.

**To add a table and enter schema information manually**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Connect data source**\.

1. On the **Connect data source** page, choose **AWS Glue Data Catalog**\.

1. Click **Next**\.

1. On the **Connection details** page, choose **Add a table and enter schema information manually**\.

1. Click **Continue to add table**\.

1. On the **Add table** page of the Athena console, for **Database**, choose an existing database or create a new one\.

1. Enter or choose a table name\.

1. For **Location of Input Data Set**, specify the path in Amazon S3 to the folder that contains the dataset that you want to process\.

1. Click **Next**\.

1. For **Data Format**, choose a data format \(**Apache Web Logs**, **CSV**, **TSV**, **Text File with Custom Delimiters**, **JSON**, **Parquet**, or **ORC**\)\.
   + For the **Apache Web Logs** option, you must also enter a regex expression in the **Regex** box\.
   + For the **Text File with Custom Delimiters** option, specify a **Field terminator** \(that is, a column delimiter\)\. Optionally, you can specify a **Collection terminator** for array types or a **Map key terminator**\.

1. For **Columns**, specify a column name and the column data type\.
   + To add more columns one at a time, choose **Add a column**\.
   + To quickly add more columns, choose **Bulk add columns**\. In the text box, enter a comma separated list of columns in the format *column\_name* *data\_type*, *column\_name* *data\_type*\[, …\], and then choose **Add**\.

1. Choose **Next**\.

1. \(Optional\) For **Partitions**, click **Add a partition** to add column names and data types\.

1. Choose **Create table**\. The DDL for the table that you specified appears in the **Query Editor**\. The following example shows the DDL generated for a two\-column table in CSV format:

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS MyManualDB.MyManualTable (
     `cola` string,
     `colb` string 
   )
   ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
   WITH SERDEPROPERTIES (
     'serialization.format' = ',',
     'field.delim' = ','
   ) LOCATION 's3://bucket_name/'
   TBLPROPERTIES ('has_encrypted_data'='false');
   ```

1. Choose **Run query** to create the table\.