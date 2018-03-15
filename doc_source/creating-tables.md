# Tables and Databases Creation Process in Athena<a name="creating-tables"></a>

You can run DDL statements in the Athena console, using a JDBC or an ODBC driver, or using the Athena **Create Table** wizard\.

When you create a new table schema in Athena, Athena stores the schema in a data catalog and uses it when you run queries\.

Athena uses an approach known as *schema\-on\-read*, which means a schema is projected on to your data at the time you execute a query\. This eliminates the need for data loading or transformation\.

 Athena does not modify your data in Amazon S3\.

Athena uses Apache Hive to define tables and create databases, which are essentially a logical namespace of tables\. 

When you create a database and table in Athena, you are simply describing the schema and the location where the table data are located in Amazon S3 for read\-time querying\. Database and table, therefore, have a slightly different meaning than they do for traditional relational database systems because the data isn't stored along with the schema definition for the database and table\. 

When you query, you query the table using standard SQL and the data is read at that time\. You can find guidance for how to create databases and tables using [Apache Hive documentation](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL), but the following provides guidance specifically for Athena\.

The maximum query string length is 256 KB\.

Hive supports multiple data formats through the use of serializer\-deserializer \(SerDe\) libraries\. You can also define complex schemas using regular expressions\. For a list of supported SerDe libraries, see [Supported Data Formats, SerDes, and Compression Formats](supported-format.md)\.

## Requirements for Tables in Athena and Data in Amazon S3<a name="s3-considerations"></a>

When you create a table, you specify an Amazon S3 bucket location for the underlying data using the `LOCATION` clause\. Consider the following:

+ You must have the appropriate permissions to work with data in the Amazon S3 location\. For more information, see [Setting User and Amazon S3 Bucket Permissions](access.md)\.

+ If the data is not encrypted in Amazon S3, it can be stored in a different region from the primary region where you run Athena\. Standard inter\-region data transfer rates for Amazon S3 apply in addition to standard Athena charges\.

+ If the data is encrypted in Amazon S3, it must be stored in the same region, and the user or principal who creates the table in Athena must have the appropriate permissions to decrypt the data\. For more information, see [Configuring Encryption Options](encryption.md)\.

+ Athena does not support different storage classes within the bucket specified by the `LOCATION` clause, does not support the `GLACIER` storage class, and does not support Requester Pays buckets\. For more information, see [Storage Classes](http://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html), [Changing the Storage Class of an Object in Amazon S3](http://docs.aws.amazon.com/AmazonS3/latest/dev/ChgStoClsOfObj.html), and [Requester Pays Buckets](http://docs.aws.amazon.com/AmazonS3/latest/dev/RequesterPaysBuckets.html) in the *Amazon Simple Storage Service Developer Guide*\.

## Functions Supported<a name="hive-ddl-functions-supported"></a>

The functions supported in Athena queries are those found within Presto\. For more information, see [Presto 0\.172 Functions and Operators](https://prestodb.io/docs/0.172/functions.html) in the Presto documentation\.

## CREATE TABLE AS Type Statements Are Not Supported<a name="create-table-as-type-statements-are-not-supported"></a>

Athena does not support CREATE TABLE AS type statements, for example, `CREATE TABLE AS SELECT`, which creates a table from the result of a SELECT query statement\.

## Transactional Data Transformations Are Not Supported<a name="transactional-data-transformations-are-not-supported"></a>

Athena does not support transaction\-based operations \(such as the ones found in Hive or Presto\) on table data\. For a full list of keywords not supported, see [Unsupported DDL](unsupported-ddl.md)\.

## Operations That Change Table States Are ACID<a name="operations-that-change-table-states-are-acid"></a>

When you create, update, or delete tables, those operations are guaranteed ACID\-compliant\. For example, if multiple users or clients attempt to create or alter an existing table at the same time, only one will be successful\.

## All Tables Are EXTERNAL<a name="all-tables-are-external"></a>

If you use `CREATE TABLE` without the `EXTERNAL` keyword, Athena issues an error; only tables with the `EXTERNAL` keyword can be created\. We recommend that you always use the `EXTERNAL` keyword\. When you drop a table in Athena, only the table metadata is removed; the data remains in Amazon S3\.

## UDF and UDAF Are Not Supported<a name="udf-and-udaf-are-not-supported"></a>

User\-defined functions \(UDF or UDAFs\) and stored procedures are not supported\.

## To create a table using the AWS Glue Data Catalog<a name="to-create-a-table-using-the-aws-glue-data-catalog"></a>

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **AWS Glue Data Catalog**\. You can now create a table with the AWS Glue Crawler\. For more information, see [Using AWS Glue Crawlers](glue-best-practices.md#schema-crawlers)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_create_table.png)

## To create a table using the wizard<a name="to-create-a-table-using-the-wizard"></a>

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Under the database display in the Query Editor, choose **Add table**, which displays a wizard\.

1. Follow the steps for creating your table\.

## To create a database using Hive DDL<a name="catalog-create-db"></a>

A database in Athena is a logical grouping for tables you create in it\.

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Query Editor**\.

1. Enter `CREATE DATABASE myDataBase` and choose **Run Query**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/createdatabase.png)

1. Select your database from the menu\. It is likely to be an empty database\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/catalogdashboard.png)

## To create a table using Hive DDL<a name="to-create-a-table-using-hive-ddl"></a>

The Athena Query Editor displays the current database\. If you create a table and don't specify a database, the table is created in the database chosen in the **Databases** section on the **Catalog** tab\.

1. In the database that you created, create a table by entering the following statement and choosing **Run Query**:

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS cloudfront_logs (
       `Date` Date,
       Time STRING,
       Location STRING,
       Bytes INT,
       RequestIP STRING,
       Method STRING,
       Host STRING,
       Uri STRING,
       Status INT,
       Referrer STRING,
       OS String,
       Browser String,
       BrowserVersion String
   ) ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
   WITH SERDEPROPERTIES (
   "input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]([^\;]+).*\%20([^\/]+)[\/](.*)$"
   ) LOCATION 's3://athena-examples/cloudfront/plaintext/';
   ```

1. If the table was successfully created, you can then run queries against your data\.