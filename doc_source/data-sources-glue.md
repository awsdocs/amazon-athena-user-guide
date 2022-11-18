# Using AWS Glue to connect to data sources in Amazon S3<a name="data-sources-glue"></a>

Athena can connect to your data stored in Amazon S3 using the AWS Glue Data Catalog to store metadata such as table and column names\. After the connection is made, your databases, tables, and views appear in Athena's query editor\.

To define schema information for AWS Glue to use, you can create an AWS Glue crawler to retrieve the information automatically, or you can manually add a table and enter the schema information\. 

## Creating an AWS Glue crawler<a name="data-sources-glue-crawler-setup"></a>

You can create a crawler by starting in the Athena console and then using the AWS Glue console in an integrated way\. When you create the crawler, you specify a data location in Amazon S3 to crawl\.

**To create a crawler in AWS Glue starting from the Athena console**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. In the query editor, next to **Tables and views**, choose **Create**, and then choose **AWS Glue crawler**\. 

1. On the **AWS Glue** console **Add crawler** page, follow the steps to create a crawler\. For more information, see [Using AWS Glue Crawlers](glue-best-practices.md#schema-crawlers) in this guide and [Populating the AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html) in the *AWS Glue Developer Guide*\.

**Note**  
Athena does not recognize [exclude patterns](https://docs.aws.amazon.com/glue/latest/dg/define-crawler.html#crawler-data-stores-exclude) that you specify for an AWS Glue crawler\. For example, if you have an Amazon S3 bucket that contains both `.csv` and `.json` files and you exclude the `.json` files from the crawler, Athena queries both groups of files\. To avoid this, place the files that you want to exclude in a different location\.

## Adding a table using a form<a name="data-sources-glue-manual-table"></a>

The following procedure shows you how to use the Athena console to add a table using the **Create Table From S3 bucket data** form\.

**To add a table and enter schema information using a form**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. In the query editor, next to **Tables and views**, choose **Create**, and then choose **S3 bucket data\.**

1. On the **Create Table From S3 bucket data** form, for **Table name**, enter a name for the table\.

1. For **Database configuration**, choose an existing database, or create a new one\.

1. For **Location of Input Data Set**, specify the path in Amazon S3 to the folder that contains the dataset that you want to process\. Do not include a file name in the path\. Athena scans all files in the folder that you specify\. If your data is already partitioned \(for example, 

    s3://*DOC\-EXAMPLE\-BUCKET*/logs/year=2004/month=12/day=11/\), enter the base path only \(for example, s3://*DOC\-EXAMPLE\-BUCKET*/logs/\)\.

1. For **Data Format**, choose among the following options:
   + For **Table type**, choose **Hive**, **Governed**, or **Iceberg**\. Athena uses the Apache Hive table type as the default\. For information about using AWS Lake Formation governed tables in Athena, see [Using governed tables](lf-governed-tables.md)\. For information about using Apache Iceberg tables in Athena, see [Using Iceberg tables](querying-iceberg.md)\.
   + For **File format**, choose the file or log format that your data is in\.
     + For the **Text File with Custom Delimiters** option, specify a **Field terminator** \(that is, a column delimiter\)\. Optionally, you can specify a **Collection terminator** that marks the end of an array type or a **Collection terminator** that marks the end of a map data type\.
   + **SerDe library** – A SerDe \(serializer\-deserializer\) library parses a particular data format so that Athena can create a table for it\. For most formats, a default SerDe library is chosen for you\. For the following formats, choose a library according to your requirements:
     + **Apache Web Logs** – Choose either the **RegexSerDe** or **GrokSerDe** library\. For RegexSerDe, provide a regular expression in the **Regex definition** box\. For GrokSerDe, provide a series of named regular expressions for the `input.format` SerDe property\. Named regular expressions are easier to read and maintain than regular expressions\. For more information, see [Querying Apache logs stored in Amazon S3](querying-apache-logs.md)\.
     + **CSV** – Choose **LazySimpleSerDe** if your comma\-separated data does not contain values enclosed in double quotes or if it uses the `java.sql.Timestamp` format\. Choose **OpenCSVSerDe** if your data includes quotes or uses the UNIX numeric format for `TIMESTAMP` \(for example, `1564610311`\)\. For more information, see [LazySimpleSerDe for CSV, TSV, and custom\-delimited files](lazy-simple-serde.md) and [OpenCSVSerDe for processing CSV](csv-serde.md)\.
     + **JSON** – Choose either the **OpenX** or **Hive** JSON SerDe library\. Both formats expect each JSON document to be on a single line of text and that fields not be separated by newline characters\. The OpenX SerDe offers some additional properties\. For more information about these properties, see [OpenX JSON SerDe](openx-json-serde.md)\. For information about the Hive SerDe, see [Hive JSON SerDe](hive-json-serde.md)\.

     For more information about using SerDe libraries in Athena, see [Supported SerDes and data formats](supported-serdes.md)\.

1. For **SerDe properties**, add, edit, or remove properties and values according to the SerDe library that you are using and your requirements\.
   + To add a SerDe property, choose **Add SerDe property**\.
   + In the **Name** field, enter the name of the property\. 
   + In the **Value** field, enter a value for the property\. 
   + To remove a SerDe property, choose **Remove**\.

1. For **Table properties**, choose or edit the table properties according to your requirements\.
   + For **Write compression**, choose a compression option\. The availability of the write compression option and of the compression options available depends on the data format\. For more information, see [Compression support in Athena by file format](compression-formats.md#compression-support-by-file-format)\.
   + For **Encryption**, select **Encrypted data set** if the underlying data is encrypted in Amazon S3\. This option sets the `has_encrypted_data` table property to true in the `CREATE TABLE` statement\.

1. For **Column details**, enter the names and data types of the columns that you want to add to the table\.
   + To add more columns one at a time, choose **Add a column**\.
   + To quickly add more columns, choose **Bulk add columns**\. In the text box, enter a comma separated list of columns in the format *column\_name* *data\_type*, *column\_name* *data\_type*\[, \.\.\.\], and then choose **Add**\.

1. \(Optional\) For **Partition details**, add one or more column names and data types\. Partitioning keeps related data together based on column values and can help reduce the amount of data scanned per query\. For information about partitioning, see [Partitioning data in Athena](partitions.md)\.

1. \(Optional\) For **Bucketing**, you can specify one or more columns that have rows that you want to group together, and then put those rows into multiple buckets\. This allows you to query only the bucket that you want to read when the bucketed columns value is specified\.
   + For **Buckets**, select one or more columns that have a large number of unique values \(for example, a primary key\) and that are frequently used to filter the data in your queries\.
   + For **Number of buckets**, enter a number that permits files to be of optimal size\. For more information, see [Top 10 Performance Tuning Tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/) in the AWS Big Data Blog\.
   + To specify your bucketed columns, the `CREATE TABLE` statement will use the following syntax:

     ```
     CLUSTERED BY (bucketed_columns) INTO number_of_buckets BUCKETS
     ```
**Note**  
The **Bucketing** option is not available for the **Governed** or **Iceberg** table types\.

1. The **Preview table query** box shows the `CREATE TABLE` statement generated by the information that you entered into the form\. The preview statement cannot be edited directly\. To change the statement, modify the form fields above the preview, or [create the statement directly](creating-tables.md#to-create-a-table-using-hive-ddl) in the query editor instead of using the form\. 

1. Choose **Create table** to run the generated statement in the query editor and create the table\.