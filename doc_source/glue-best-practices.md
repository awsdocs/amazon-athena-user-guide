# Best practices when using Athena with AWS Glue<a name="glue-best-practices"></a>

When using Athena with the AWS Glue Data Catalog, you can use AWS Glue to create databases and tables \(schema\) to be queried in Athena, or you can use Athena to create schema and then use them in AWS Glue and related services\. This topic provides considerations and best practices when using either method\.

Under the hood, Athena uses Trino to process DML statements and Hive to process the DDL statements that create and modify schema\. With these technologies, there are a couple of conventions to follow so that Athena and AWS Glue work well together\.

 **In this topic** 
+  **[Database, table, and column names](#schema-names)** 
+   
** [Using AWS Glue crawlers](#schema-crawlers) **  
  +  [Scheduling a crawler to keep the AWS Glue Data Catalog and Amazon S3 in sync](#schema-crawlers-schedule) 
  +  [Using multiple data sources with crawlers](#schema-crawlers-data-sources) 
  +  [Syncing partition schema to avoid "HIVE\_PARTITION\_SCHEMA\_MISMATCH"](#schema-syncing) 
  +  [Updating table metadata](#schema-table-metadata) 
+   
** [Working with CSV files](#schema-csv) **  
  +  [CSV data enclosed in quotes](#schema-csv-quotes) 
  +  [CSV files with headers](#schema-csv-headers) 
+ ** [AWS Glue partition indexing and filtering](#glue-best-practices-partition-index)**
+ **[Working with geospatial data](#schema-geospatial)**
+   
** [Using AWS Glue jobs for ETL with Athena](#schema-classifier) **  
  +  [Creating tables using Athena for AWS Glue ETL jobs](#schema-etl-tables) 
  +  [Using ETL jobs to optimize query performance](#schema-etl-performance) 
  +  [Converting SMALLINT and TINYINT data types to INT when converting to ORC](#schema-etl-orc) 
  +  [Automating AWS Glue jobs for ETL](#schema-etl-automate) 

## Database, table, and column names<a name="schema-names"></a>

When you create schema in AWS Glue to query in Athena, consider the following:
+ A database name cannot be longer than 255 characters\.
+ A table name cannot be longer than 255 characters\.
+ A column name cannot be longer than 255 characters\.
+ The only acceptable characters for database names, table names, and column names are lowercase letters, numbers, and the underscore character\.

For more information, see [Databases](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api-catalog-databases.html) and [Tables](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api-catalog-tables.html) in the *AWS Glue Developer Guide*\.

**Note**  
If you use an [AWS::Glue::Database](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-database.html) AWS CloudFormation template to create an AWS Glue database and do not specify a database name, AWS Glue automatically generates a database name in the format *resource\_name–random\_string* that is not compatible with Athena\.

You can use the AWS Glue Catalog Manager to rename columns, but not table names or database names\. To work around this limitation, you must use a definition of the old database to create a database with the new name\. Then you use definitions of the tables from the old database to re\-create the tables in the new database\. To do this, you can use the AWS CLI or AWS Glue SDK\. For steps, see [Using the AWS CLI to recreate an AWS Glue database and its tables](glue-recreate-db-and-tables-cli.md)\.

## Using AWS Glue crawlers<a name="schema-crawlers"></a>

AWS Glue crawlers help discover the schema for datasets and register them as tables in the AWS Glue Data Catalog\. The crawlers go through your data and determine the schema\. In addition, the crawler can detect and register partitions\. For more information, see [Defining crawlers](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html) in the *AWS Glue Developer Guide*\. Tables from data that were successfully crawled can be queried from Athena\.

**Note**  
Athena does not recognize [exclude patterns](https://docs.aws.amazon.com/glue/latest/dg/define-crawler.html#crawler-data-stores-exclude) that you specify for an AWS Glue crawler\. For example, if you have an Amazon S3 bucket that contains both `.csv` and `.json` files and you exclude the `.json` files from the crawler, Athena queries both groups of files\. To avoid this, place the files that you want to exclude in a different location\. 

### Scheduling a crawler to keep the AWS Glue Data Catalog and Amazon S3 in sync<a name="schema-crawlers-schedule"></a>

AWS Glue crawlers can be set up to run on a schedule or on demand\. For more information, see [Time\-based schedules for jobs and crawlers](https://docs.aws.amazon.com/glue/latest/dg/monitor-data-warehouse-schedule.html) in the *AWS Glue Developer Guide*\.

If you have data that arrives for a partitioned table at a fixed time, you can set up an AWS Glue crawler to run on schedule to detect and update table partitions\. This can eliminate the need to run a potentially long and expensive `MSCK REPAIR` command or manually run an `ALTER TABLE ADD PARTITION` command\. For more information, see [Table partitions](https://docs.aws.amazon.com/glue/latest/dg/tables-described.html#tables-partition) in the *AWS Glue Developer Guide*\.

### Using multiple data sources with crawlers<a name="schema-crawlers-data-sources"></a>

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

### To add an S3 data store to an existing crawler in AWS Glue<a name="to-add-another-data-store-to-an-existing-crawler-in-aws-glue"></a>

1. Sign in to the AWS Management Console and open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. In the navigation pane, choose **Crawlers**\.

1. Choose the link to your crawler, and then choose **Edit**\. 

1. For **Step 2: Choose data sources and classifiers**, choose **Edit**\. 

1. For **Data sources**, choose **Add a data source**\.

1. In the **Add data source** dialog box, for **S3 path**, choose **Browse**\. 

1. Select the bucket that you want to use, and then choose **Choose**\.

   The data source that you added appears in the **Data sources** list\.

1. Choose **Next**\.

1. On the **Configure security settings** page, create or choose an IAM role for the crawler, and then choose **Next**\.

1. Make sure that the S3 path ends in a trailing slash, and then choose **Add an S3 data source**\.

1. On the **Set output and scheduling** page, for **Output configuration**, choose the target database\.

1. Choose **Next**\.

1. On the **Review and update** page, review the choices that you made\. To edit a step, choose **Edit**\.

1.  Choose **Update**\.

### Syncing partition schema to avoid "HIVE\_PARTITION\_SCHEMA\_MISMATCH"<a name="schema-syncing"></a>

For each table within the AWS Glue Data Catalog that has partition columns, the schema is stored at the table level and for each individual partition within the table\. The schema for partitions are populated by an AWS Glue crawler based on the sample of data that it reads within the partition\. For more information, see [Using multiple data sources with crawlers](#schema-crawlers-data-sources)\.

When Athena runs a query, it validates the schema of the table and the schema of any partitions necessary for the query\. The validation compares the column data types in order and makes sure that they match for the columns that overlap\. This prevents unexpected operations such as adding or removing columns from the middle of a table\. If Athena detects that the schema of a partition differs from the schema of the table, Athena may not be able to process the query and fails with `HIVE_PARTITION_SCHEMA_MISMATCH`\.

There are a few ways to fix this issue\. First, if the data was accidentally added, you can remove the data files that cause the difference in schema, drop the partition, and re\-crawl the data\. Second, you can drop the individual partition and then run `MSCK REPAIR` within Athena to re\-create the partition using the table's schema\. This second option works only if you are confident that the schema applied will continue to read the data correctly\.

### Updating table metadata<a name="schema-table-metadata"></a>

After a crawl, the AWS Glue crawler automatically assigns certain table metadata to help make it compatible with other external technologies like Apache Hive, Presto, and Spark\. Occasionally, the crawler may incorrectly assign metadata properties\. Manually correct the properties in AWS Glue before querying the table using Athena\. For more information, see [Viewing and editing table details](https://docs.aws.amazon.com/glue/latest/dg/console-tables.html#console-tables-details) in the *AWS Glue Developer Guide*\.

AWS Glue may mis\-assign metadata when a CSV file has quotes around each data field, getting the `serializationLib` property wrong\. For more information, see [CSV data enclosed in quotes](#schema-csv-quotes)\.

## Working with CSV files<a name="schema-csv"></a>

CSV files occasionally have quotes around the data values intended for each column, and there may be header values included in CSV files, which aren't part of the data to be analyzed\. When you use AWS Glue to create schema from these files, follow the guidance in this section\.

### CSV data enclosed in quotes<a name="schema-csv-quotes"></a>

You might have a CSV file that has data fields enclosed in double quotes like the following example:

```
"John","Doe","123-555-1231","John said \"hello\""
"Jane","Doe","123-555-9876","Jane said \"hello\""
```

To run a query in Athena on a table created from a CSV file that has quoted values, you must modify the table properties in AWS Glue to use the OpenCSVSerDe\. For more information about the OpenCSV SerDe, see [OpenCSVSerDe for processing CSV](csv-serde.md)\.

**To edit table properties in the AWS Glue console**

1. In the AWS Glue console navigation pane, choose **Tables**\.

1. Choose the link for the table that you want to edit, and then choose **Actions**, **Edit table**\.

1. On the **Edit table** page, make the following changes:
   + For **Serialization lib**, enter `org.apache.hadoop.hive.serde2.OpenCSVSerde`\.
   + For **Serde parameters**, enter the following values for the keys `escapeChar`, `quoteChar`, and `separatorChar`: 
     + For `escapeChar`, enter a backslash \(**\\**\)\.
     + For `quoteChar`, enter a double quote \(**"**\)\.
     + For `separatorChar`, enter a comma \(**,**\)\.

1. Choose **Save**\.

For more information, see [Viewing and editing table details](https://docs.aws.amazon.com/glue/latest/dg/console-tables.html#console-tables-details) in the *AWS Glue Developer Guide*\.

#### Updating AWS Glue table properties programmatically<a name="schema-csv-quotes-api"></a>

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

### CSV files with headers<a name="schema-csv-headers"></a>

When you define a table in Athena with a `CREATE TABLE` statement, you can use the `skip.header.line.count` table property to ignore headers in your CSV data, as in the following example\.

```
...
STORED AS TEXTFILE
LOCATION 's3://my_bucket/csvdata_folder/';
TBLPROPERTIES ("skip.header.line.count"="1")
```

Alternatively, you can remove the CSV headers beforehand so that the header information is not included in Athena query results\. One way to achieve this is to use AWS Glue jobs, which perform extract, transform, and load \(ETL\) work\. You can write scripts in AWS Glue using a language that is an extension of the PySpark Python dialect\. For more information, see [Authoring Jobs in AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/author-job-glue.html) in the *AWS Glue Developer Guide*\.

The following example shows a function in an AWS Glue script that writes out a dynamic frame using `from_options`, and sets the `writeHeader` format option to false, which removes the header information:

```
glueContext.write_dynamic_frame.from_options(frame = applymapping1, connection_type = "s3", connection_options = {"path": "s3://MYBUCKET/MYTABLEDATA/"}, format = "csv", format_options = {"writeHeader": False}, transformation_ctx = "datasink2")
```

## AWS Glue partition indexing and filtering<a name="glue-best-practices-partition-index"></a>

When Athena queries partitioned tables, it retrieves and filters the available table partitions to the subset relevant to your query\. As new data and partitions are added, more time is required to process the partitions, and query runtime can increase\. If you have a table with a large number of partitions that grows over time, consider using AWS Glue partition indexing and filtering\. Partition indexing allows Athena to optimize partition processing and improve query performance on highly partitioned tables\. Setting up partition filtering in a table's properties is a two\-step process:

1. Creating a partition index in AWS Glue\.

1. Enabling partition filtering for the table\.

### Creating a partition index<a name="glue-best-practices-partition-index-creating"></a>

For steps on creating a partition index in AWS Glue, see [Working with partition indexes](https://docs.aws.amazon.com/glue/latest/dg/partition-indexes.html) in the AWS Glue Developer Guide\. For the limitations on partition indexes in AWS Glue, see the [About partition indexes](https://docs.aws.amazon.com/glue/latest/dg/partition-indexes.html#partition-index-1) section on that page\.

### Enabling partition filtering<a name="glue-best-practices-partition-filtering-enabling"></a>

To enable partition filtering for the table, you must set a new table property in AWS Glue\. For steps on how to set table properties in AWS Glue, refer to the [Setting up partition projection](https://docs.aws.amazon.com/athena/latest/ug/partition-projection-setting-up.html) page\. When you edit the table details in AWS Glue, add the following key\-value pair to the **Table properties** section:
+ For **Key**, add `partition_filtering.enabled`
+ For **Value**, add `true`

You can disable partition filtering on this table at any time by setting the `partition_filtering.enabled` value to `false`\.

After you complete the above steps, you can return to the Athena console to query the data\.

## Working with geospatial data<a name="schema-geospatial"></a>

AWS Glue does not natively support Well\-known Text \(WKT\), Well\-Known Binary \(WKB\), or other PostGIS data types\. The AWS Glue classifier parses geospatial data and classifies them using supported data types for the format, such as `varchar` for CSV\. As with other AWS Glue tables, you may need to update the properties of tables created from geospatial data to allow Athena to parse these data types as\-is\. For more information, see [Using AWS Glue crawlers](#schema-crawlers) and [Working with CSV files](#schema-csv)\. Athena may not be able to parse some geospatial data types in AWS Glue tables as\-is\. For more information about working with geospatial data in Athena, see [Querying geospatial data](querying-geospatial-data.md)\.

## Using AWS Glue jobs for ETL with Athena<a name="schema-classifier"></a>

AWS Glue jobs perform ETL operations\. An AWS Glue job runs a script that extracts data from sources, transforms the data, and loads it into targets\. For more information, see [Authoring Jobs in AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/author-job-glue.html) in the *AWS Glue Developer Guide*\.

### Creating tables using Athena for AWS Glue ETL jobs<a name="schema-etl-tables"></a>

Tables that you create in Athena must have a table property added to them called a `classification`, which identifies the format of the data\. This allows AWS Glue to use the tables for ETL jobs\. The classification values can be `avro`, `csv`, `json`, `orc`, `parquet`, or `xml`\. An example `CREATE TABLE` statement in Athena follows:

```
CREATE EXTERNAL TABLE sampleTable (
  column1 INT,
  column2 INT
  ) STORED AS PARQUET
  TBLPROPERTIES (
  'classification'='parquet')
```

If the table property was not added when the table was created, you can add it using the AWS Glue console\.

### To add the classification table property using the AWS Glue console<a name="to-change-the-classification-property-using-the-console"></a>

1. Sign in to the AWS Management Console and open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. In the console navigation pane, choose **Tables**\.

1. Choose the link for the table that you want to edit, and then choose **Actions**, **Edit table**\.

1. Scroll down to the **Table properties** section\.

1. Choose **Add**\.

1. For **Key**, enter **classification**\.

1. For **Value**, enter a data type \(for example, **json**\)\.

1. Choose **Save**\.

   In the **Table details** section, the data type that you entered appears in the **Classification** field for the table\.

For more information, see [Working with tables](https://docs.aws.amazon.com/glue/latest/dg/console-tables.html) in the *AWS Glue Developer Guide*\.

### Using ETL jobs to optimize query performance<a name="schema-etl-performance"></a>

AWS Glue jobs can help you transform data to a format that optimizes query performance in Athena\. Data formats have a large impact on query performance and query costs in Athena\.

We recommend to use Parquet and ORC data formats\. AWS Glue supports writing to both of these data formats, which can make it easier and faster for you to transform data to an optimal format for Athena\. For more information about these formats and other ways to improve performance, see [Top performance tuning tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.

### Converting SMALLINT and TINYINT data types to INT when converting to ORC<a name="schema-etl-orc"></a>

To reduce the likelihood that Athena is unable to read the `SMALLINT` and `TINYINT` data types produced by an AWS Glue ETL job, convert `SMALLINT` and `TINYINT` to `INT` when using the wizard or writing a script for an ETL job\.

### Automating AWS Glue jobs for ETL<a name="schema-etl-automate"></a>

You can configure AWS Glue ETL jobs to run automatically based on triggers\. This feature is ideal when data from outside AWS is being pushed to an Amazon S3 bucket in a suboptimal format for querying in Athena\. For more information, see [Triggering AWS Glue jobs](https://docs.aws.amazon.com/glue/latest/dg/trigger-job.html) in the *AWS Glue Developer Guide*\.