# Best Practices When Using Athena with AWS Glue<a name="glue-best-practices"></a>

When using Athena with the AWS Glue Data Catalog, you can use AWS Glue to create databases and tables \(schema\) to be queried in Athena, or you can use Athena to create schema and then use them in AWS Glue and related services\. This topic provides considerations and best practices when using either method\.

Under the hood, Athena uses Presto to process DML statements and Hive to process the DDL statements that create and modify schema\. With these technologies, there are a couple of conventions to follow so that Athena and AWS Glue work well together\.

 **In this topic** 
+  **[Database, Table, and Column Names](#schema-names)** 
+   
** [Using AWS Glue Crawlers](#schema-crawlers) **  
  +  [Scheduling a Crawler to Keep the AWS Glue Data Catalog and Amazon S3 in Sync](#schema-crawlers-schedule) 
  +  [Using Multiple Data Sources with Crawlers](#schema-crawlers-data-sources) 
  +  [Syncing Partition Schema to Avoid "HIVE\_PARTITION\_SCHEMA\_MISMATCH"](#schema-syncing) 
  +  [Updating Table Metadata](#schema-table-metadata) 
+   
** [Working with CSV Files](#schema-csv) **  
  +  [CSV Data Enclosed in Quotes](#schema-csv-quotes) 
  +  [CSV Files with Headers](#schema-csv-headers) 
+ **[Working with Geospatial Data](#schema-geospatial)**
+   
** [Using AWS Glue Jobs for ETL with Athena](#schema-classifier) **  
  +  [Creating Tables Using Athena for AWS Glue ETL Jobs](#schema-etl-tables) 
  +  [Using ETL Jobs to Optimize Query Performance](#schema-etl-performance) 
  +  [Converting SMALLINT and TINYINT Datatypes to INT When Converting to ORC](#schema-etl-orc) 
  +  [Automating AWS Glue Jobs for ETL](#schema-etl-automate) 

## Database, Table, and Column Names<a name="schema-names"></a>

When you create schema in AWS Glue to query in Athena, consider the following:
+ A database name cannot be longer than 255 characters\.
+ A table name cannot be longer than 255 characters\.
+ A column name cannot be longer than 255 characters\.
+ The only acceptable characters for database names, table names, and column names are lowercase letters, numbers, and the underscore character\.

For more information, see [Databases](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api-catalog-databases.html) and [Tables](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api-catalog-tables.html) in the *AWS Glue Developer Guide*\.

You can use the AWS Glue Catalog Manager to rename columns, but not table names or database names\. To change a database name, you must create a new database and copy tables from the old database to it \(in other words, copy the metadata to a new entity\)\. You can follow a similar process for tables\. You can use the AWS Glue SDK or AWS CLI to do this\.

## Using AWS Glue Crawlers<a name="schema-crawlers"></a>

AWS Glue crawlers help discover and register the schema for datasets in the AWS Glue Data Catalog\. The crawlers go through your data, and inspect portions of it to determine the schema\. In addition, the crawler can detect and register partitions\. For more information, see [Cataloging Data with a Crawler](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html) in the *AWS Glue Developer Guide*\.

**Note**  
Athena does not recognize [exclude patterns](https://docs.aws.amazon.com/glue/latest/dg/define-crawler.html#crawler-data-stores-exclude) that you specify for an AWS Glue crawler\. For example, if you have an Amazon S3 bucket that contains both `.csv` and `.json` files and you exclude the `.json` files from the crawler, Athena queries both groups of files\. To avoid this, place the files that you want to exclude in a different location\. 

### Scheduling a Crawler to Keep the AWS Glue Data Catalog and Amazon S3 in Sync<a name="schema-crawlers-schedule"></a>

AWS Glue crawlers can be set up to run on a schedule or on demand\. For more information, see [Time\-Based Schedules for Jobs and Crawlers](https://docs.aws.amazon.com/glue/latest/dg/monitor-data-warehouse-schedule.html) in the *AWS Glue Developer Guide*\.

If you have data that arrives for a partitioned table at a fixed time, you can set up an AWS Glue crawler to run on schedule to detect and update table partitions\. This can eliminate the need to run a potentially long and expensive `MSCK REPAIR` command or manually run an `ALTER TABLE ADD PARTITION` command\. For more information, see [Table Partitions](https://docs.aws.amazon.com/glue/latest/dg/tables-described.html#tables-partition) in the *AWS Glue Developer Guide*\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue-crawler.png)

### Using Multiple Data Sources with Crawlers<a name="schema-crawlers-data-sources"></a>

When an AWS Glue crawler scans Amazon S3 and detects multiple directories, it uses a heuristic to determine where the root for a table is in the directory structure, and which directories are partitions for the table\. In some cases, where the schema detected in two or more directories is similar, the crawler may treat them as partitions instead of separate tables\. One way to help the crawler discover individual tables is to add each table's root directory as a data store for the crawler\.

The following partitions in Amazon S3 are an example:

```
s3://bucket01/folder1/table1/partition1/file.txt
s3://bucket01/folder1/table1/partition2/file.txt
s3://bucket01/folder1/table1/partition3/file.txt
s3://bucket01/folder1/table2/partition4/file.txt
s3://bucket01/folder1/table2/partition5/file.txt
```

If the schema for `table1` and `table2` are similar, and a single data source is set to `s3://bucket01/folder1/` in AWS Glue, the crawler may create a single table with two partition columns: one partition column that contains `table1` and `table2`, and a second partition column that contains `partition1` through `partition5`\.

To have the AWS Glue crawler create two separate tables, set the crawler to have two data sources, `s3://bucket01/folder1/table1/` and `s3://bucket01/folder1/table2`, as shown in the following procedure\.

### To add another data store to an existing crawler in AWS Glue<a name="to-add-another-data-store-to-an-existing-crawler-in-aws-glue"></a>

1. Sign in to the AWS Management Console and open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. Choose **Crawlers**, select your crawler, and then choose **Action**, **Edit crawler**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_add_data_store0.png)

1. Under **Add information about your crawler**, choose additional settings as appropriate, and then choose **Next**\.

1. Under **Add a data store**, change **Include path** to the table\-level directory\. For instance, given the example above, you would change it from `s3://bucket01/folder1 to s3://bucket01/folder1/table1/`\. Choose **Next**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_add_data_store1.png)

1. For **Add another data store**, choose **Yes**, **Next**\.

1. For **Include path**, enter your other table\-level directory \(for example, `s3://bucket01/folder1/table2/`\) and choose **Next**\.

   1. Repeat steps 3\-5 for any additional table\-level directories, and finish the crawler configuration\.

The new values for **Include locations** appear under data stores as follows:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_add_data_store2.png)

### Syncing Partition Schema to Avoid "HIVE\_PARTITION\_SCHEMA\_MISMATCH"<a name="schema-syncing"></a>

For each table within the AWS Glue Data Catalog that has partition columns, the schema is stored at the table level and for each individual partition within the table\. The schema for partitions are populated by an AWS Glue crawler based on the sample of data that it reads within the partition\. For more information, see [Using Multiple Data Sources with Crawlers](#schema-crawlers-data-sources)\.

When Athena runs a query, it validates the schema of the table and the schema of any partitions necessary for the query\. The validation compares the column data types in order and makes sure that they match for the columns that overlap\. This prevents unexpected operations such as adding or removing columns from the middle of a table\. If Athena detects that the schema of a partition differs from the schema of the table, Athena may not be able to process the query and fails with `HIVE_PARTITION_SCHEMA_MISMATCH`\.

There are a few ways to fix this issue\. First, if the data was accidentally added, you can remove the data files that cause the difference in schema, drop the partition, and re\-crawl the data\. Second, you can drop the individual partition and then run `MSCK REPAIR` within Athena to re\-create the partition using the table's schema\. This second option works only if you are confident that the schema applied will continue to read the data correctly\.

### Updating Table Metadata<a name="schema-table-metadata"></a>

After a crawl, the AWS Glue crawler automatically assigns certain table metadata to help make it compatible with other external technologies like Apache Hive, Presto, and Spark\. Occasionally, the crawler may incorrectly assign metadata properties\. Manually correct the properties in AWS Glue before querying the table using Athena\. For more information, see [Viewing and Editing Table Details](https://docs.aws.amazon.com/glue/latest/dg/console-tables.html#console-tables-details) in the *AWS Glue Developer Guide*\.

AWS Glue may mis\-assign metadata when a CSV file has quotes around each data field, getting the `serializationLib` property wrong\. For more information, see [CSV Data Enclosed in quotes](#schema-csv-quotes)\.

## Working with CSV Files<a name="schema-csv"></a>

CSV files occasionally have quotes around the data values intended for each column, and there may be header values included in CSV files, which aren't part of the data to be analyzed\. When you use AWS Glue to create schema from these files, follow the guidance in this section\.

### CSV Data Enclosed in Quotes<a name="schema-csv-quotes"></a>

You might have a CSV file that has data fields enclosed in double quotes like the following example:

```
"John","Doe","123-555-1231","John said \"hello\""
"Jane","Doe","123-555-9876","Jane said \"hello\""
```

To run a query in Athena on a table created from a CSV file that has quoted values, you must modify the table properties in AWS Glue to use the OpenCSVSerDe\. For more information about the OpenCSV SerDe, see [OpenCSVSerDe for Processing CSV](csv-serde.md)\.

**To edit table properties in the AWS Glue console**

1. In the AWS Glue console navigation pane, choose **Tables**\.

1. Choose the table that you want to edit, and then choose **Edit table**\.

1. In the **Edit table details** dialog box, make the following changes:
   + For **Serde serialization lib**, enter `org.apache.hadoop.hive.serde2.OpenCSVSerde`\.
   + For **Serde parameters**, enter the following values for the keys `escapeChar`, `quoteChar`, and `separatorChar`: 
     + For `escapeChar`, enter a backslash \(**\\**\)\.
     + For `quoteChar`, enter a double quote \(**"**\)\.
     + For `separatorChar`, enter a comma \(**,**\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_edit_serde.png)

For more information, see [Viewing and Editing Table Details](https://docs.aws.amazon.com/glue/latest/dg/console-tables.html#console-tables-details) in the *AWS Glue Developer Guide*\.

#### Updating AWS Glue Table Properties Programmatically<a name="schema-csv-quotes-api"></a>

You can use the AWS Glue [UpdateTable](https://docs.aws.amazon.com/glue/latest/webapi/API_UpdateTable.html) API operation or [update\-table](https://docs.aws.amazon.com/cli/latest/reference/glue/update-table.html) CLI command to modify the `SerDeInfo` block in the table definition, as in the following example JSON\.

```
"SerDeInfo": {
   "name": "",
   "serializationLib": "org.apache.hadoop.hive.serde2.OpenCSVSerde",
   "parameters": {
      "separatorChar": ","
      "quoteChar": "\""
      "escapeChar": "\\"
      }
},
```

### CSV Files with Headers<a name="schema-csv-headers"></a>

When you define a table in Athena with a `CREATE TABLE` statement, you can use the `skip.header.line.count` table property to ignore headers in your CSV data, as in the following example\.

```
...
STORED AS TEXTFILE
LOCATION 's3://my_bucket/csvdata_folder/';
TBLPROPERTIES ("skip.header.line.count"="1")
```

Alternatively, you can remove the CSV headers beforehand so that the header information is not included in Athena query results\. One way to achieve this is to use AWS Glue jobs, which perform extract, transform, and load \(ETL\) work\. You can write scripts in AWS Glue using a language that is an extension of the PySpark Python dialect\. For more information, see [Authoring Jobs in Glue](https://docs.aws.amazon.com/glue/latest/dg/busisadd-job.html) in the *AWS Glue Developer Guide*\.

The following example shows a function in an AWS Glue script that writes out a dynamic frame using `from_options`, and sets the `writeHeader` format option to false, which removes the header information:

```
glueContext.write_dynamic_frame.from_options(frame = applymapping1, connection_type = "s3", connection_options = {"path": "s3://MYBUCKET/MYTABLEDATA/"}, format = "csv", format_options = {"writeHeader": False}, transformation_ctx = "datasink2")
```

## Working with Geospatial Data<a name="schema-geospatial"></a>

AWS Glue does not natively support Well\-known Text \(WKT\), Well\-Known Binary \(WKB\), or other PostGIS data types\. The AWS Glue classifier parses geospatial data and classifies them using supported data types for the format, such as `varchar` for CSV\. As with other AWS Glue tables, you may need to update the properties of tables created from geospatial data to allow Athena to parse these data types as\-is\. For more information, see [Using AWS Glue Crawlers](#schema-crawlers) and [Working with CSV Files](#schema-csv)\. Athena may not be able to parse some geospatial data types in AWS Glue tables as\-is\. For more information about working with geospatial data in Athena, see [Querying Geospatial Data](querying-geospatial-data.md)\.

## Using AWS Glue Jobs for ETL with Athena<a name="schema-classifier"></a>

AWS Glue jobs perform ETL operations\. An AWS Glue job runs a script that extracts data from sources, transforms the data, and loads it into targets\. For more information, see [Authoring Jobs in Glue](https://docs.aws.amazon.com/glue/latest/dg/busisadd-job.html) in the *AWS Glue Developer Guide*\.

### Creating Tables Using Athena for AWS Glue ETL Jobs<a name="schema-etl-tables"></a>

Tables that you create in Athena must have a table property added to them called a `classification`, which identifies the format of the data\. This allows AWS Glue to use the tables for ETL jobs\. The classification values can be `csv`, `parquet`, `orc`, `avro`, or `json`\. An example `CREATE TABLE` statement in Athena follows:

```
CREATE EXTERNAL TABLE sampleTable (
  column1 INT,
  column2 INT
  ) STORED AS PARQUET
  TBLPROPERTIES (
  'classification'='parquet')
```

If the table property was not added when the table was created, you can add it using the AWS Glue console\.

### To change the classification property using the console<a name="to-change-the-classification-property-using-the-console"></a>

1.   
**Choose **Edit Table**\.**  

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_edit_table.png)

1.   
**For **Classification**, select the file type and choose **Apply**\.**  

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_edit_table_classification.png)

For more information, see [Working with Tables](https://docs.aws.amazon.com/glue/latest/dg/console-tables.html) in the *AWS Glue Developer Guide*\.

### Using ETL Jobs to Optimize Query Performance<a name="schema-etl-performance"></a>

AWS Glue jobs can help you transform data to a format that optimizes query performance in Athena\. Data formats have a large impact on query performance and query costs in Athena\.

We recommend to use Parquet and ORC data formats\. AWS Glue supports writing to both of these data formats, which can make it easier and faster for you to transform data to an optimal format for Athena\. For more information about these formats and other ways to improve performance, see [Top Performance Tuning Tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.

### Converting SMALLINT and TINYINT Data Types to INT When Converting to ORC<a name="schema-etl-orc"></a>

To reduce the likelihood that Athena is unable to read the `SMALLINT` and `TINYINT` data types produced by an AWS Glue ETL job, convert `SMALLINT` and `TINYINT` to `INT` when using the wizard or writing a script for an ETL job\.

### Automating AWS Glue Jobs for ETL<a name="schema-etl-automate"></a>

You can configure AWS Glue ETL jobs to run automatically based on triggers\. This feature is ideal when data from outside AWS is being pushed to an Amazon S3 bucket in a suboptimal format for querying in Athena\. For more information, see [Triggering AWS Glue Jobs](https://docs.aws.amazon.com/glue/latest/dg/trigger-job.html) in the *AWS Glue Developer Guide*\.