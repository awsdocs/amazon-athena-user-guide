# Getting Started<a name="getting-started"></a>

This tutorial walks you through using Amazon Athena to query data\. You'll create a table based on sample data stored in Amazon Simple Storage Service, query the table, and check the results of the query\.

The tutorial is using live resources, so you are charged for the queries that you run\. You aren't charged for the sample data in the location that this tutorial uses, but if you upload your own data files to Amazon S3, charges do apply\.

## Prerequisites<a name="prerequisites"></a>
+ If you have not already done so, sign up for an account in [Setting Up](setting-up.md)\.
+ Using the same AWS Region \(for example, US West \(Oregon\)\) and account that you are using for Athena, [Create a bucket in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) to hold your query results from Athena\.

## Step 1: Create a Database<a name="step-1-create-a-database"></a>

You first need to create a database in Athena\.

**To create a database**

1. Open the Athena console\.

1. If this is your first time visiting the Athena console, you'll go to a Getting Started page\. Choose **Get Started** to open the Query Editor\. If it isn't your first time, the Athena Query Editor opens\.

1. Choose the link to **set up a query result location in Amazon S3**\.  
![\[Choose set up a query result location.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-choose-set-up-query-location.png)

1. In the **Settings** dialog box, enter the path to the bucket that you created in Amazon S3 for your query results\. Prefix the path with `s3://` and add a forward slash to the end of the path\.  
![\[Specify a location in Amazon S3 to receive query results from Athena.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-setting-results-location.png)

1. Click **Save**\.

1. In the Athena Query Editor, you see a query pane\. You can type queries and statements here\.  
![\[The query pane in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/example_query.png)

1. To create a database named `mydatabase`, enter the following CREATE DATABASE statement\.

   ```
   CREATE DATABASE mydatabase
   ```

1. Choose **Run Query** or press **Ctrl\+ENTER**\.

1. Confirm that the catalog display refreshes and `mydatabase` appears in the **Database** list in the navigation pane on the left\.  
![\[Created database appears in the Athena console navigation pane.\]](http://docs.aws.amazon.com/athena/latest/ug/images/db_list_and_refresh.png)

## Step 2: Create a Table<a name="step-2-create-a-table"></a>

Now that you have a database, you can create an Athena table for it\. The table that you create will be based on Athena sample data in the location `s3://athena-examples-myregion/cloudfront/plaintext/`\. 

The sample log data is in tab\-separated values \(TSV\) format, which means that a tab character is used as a delimiter to separate the fields\. The data looks like the following example\. For readability, the tabs in the excerpt have been converted to spaces and the final field shortened\. 

```
2014-07-05 20:00:09 DFW3 4260 10.0.0.15 GET eabcd12345678.cloudfront.net /test-image-1.jpeg 200 - Mozilla/5.0[...]
2014-07-05 20:00:09 DFW3 4252 10.0.0.15 GET eabcd12345678.cloudfront.net /test-image-2.jpeg 200 - Mozilla/5.0[...]
2014-07-05 20:00:10 AMS1 4261 10.0.0.15 GET eabcd12345678.cloudfront.net /test-image-3.jpeg 200 - Mozilla/5.0[...]
```

To enable Athena to read this data, you run a `CREATE EXTERNAL TABLE` statement like the following\. The statement that creates the table defines columns that map to the data, specifies how the data is delimited, and specifies the Amazon S3 location that contains the sample data\. 

```
CREATE EXTERNAL TABLE IF NOT EXISTS cloudfront_logs (
  `Date` DATE,
  Time STRING,
  Location STRING,
  Bytes INT,
  RequestIP STRING,
  Method STRING,
  Host STRING,
  Uri STRING,
  Status INT,
  Referrer STRING,
  ClientInfo STRING
  ) 
  ROW FORMAT DELIMITED
  FIELDS TERMINATED BY '\t'
  LINES TERMINATED BY '\n'
  LOCATION 's3://athena-examples-my-region/cloudfront/plaintext/';
```

The example creates a table called `cloudfront_logs` and specifies a name and data type for each field\. These fields will become the columns in the table\. Because `date` is a [reserved word](reserved-words.md#list-of-ddl-reserved-words), it is escaped with backtick \(`\) characters\. `ROW FORMAT DELIMITED` means that Athena will use a default library called [LazySimpleSerDe](lazy-simple-serde.md) to do the actual work of parsing the data\. The example also specifies that the fields are tab separated \(`FIELDS TERMINATED BY '\t'`\) and that each record in the file ends in a newline character \(`LINES TERMINATED BY '\n`\)\. Finally, the `LOCATION` clause specifies the path in Amazon S3 where the actual data to be read is located\. You can use a `CREATE TABLE` statement like this if you have your own tab or comma\-separated data\.

Returning to our sample data, here is the full example data for the `ClientInfo` field that was previously shortened:

```
Mozilla/5.0%20(Android;%20U;%20Windows%20NT%205.1;%20en-US;%20rv:1.9.0.9)%20Gecko/2009040821%20IE/3.0.9
```

As you can see, this one field is multivalued\. If tabs are used as field delimiters, the separate components inside this field can't be broken out into separate columns\. To create columns from the values inside the field, you can use a regular expression \(regex\) that contains regex groups\. The regex groups that you specify become separate table columns\. The new `CREATE TABLE` statement will use syntax like the following, which instructs Athena to use the [Regex SerDe](regex-serde.md) library\.

```
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
  WITH SERDEPROPERTIES ("input.regex" = "regular_expression")
```

Regular expressions can be useful for creating tables from complex CSV or TSV data but can be difficult to write and maintain\. Fortunately, there are other libraries that you can use for formats like JSON, Parquet, and ORC\. For more information, see [Supported SerDes and Data Formats](supported-serdes.md)\.

Now you are ready to create the table in the Athena Query Editor\. You'll use the regex in a statement that is provided for you\.

**To create a table**

1. For **Database**, choose `mydatabase`\.

1. Choose the plus \(**\+**\) sign in the Query Editor to create a tab with a new query\. You can have up to ten query tabs open at once\.  
![\[Choose the plus icon to create a new query.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-new-query-tab.png)

1. In the query pane, enter the following `CREATE EXTERNAL TABLE` statement\. In the `LOCATION` statement at the end of the query, replace *myregion* with the AWS Region that you are currently using \(for example, `us-west-1`\)\. The regex expression makes it possible to break out the operating system, browser, and browser version information from the final tab\-separated field in the log data\.

   ```
   CREATE EXTERNAL TABLE IF NOT EXISTS cloudfront_logs (
     `Date` DATE,
     Time STRING,
     Location STRING,
     Bytes INT,
     RequestIP STRING,
     Method STRING,
     Host STRING,
     Uri STRING,
     Status INT,
     Referrer STRING,
     os STRING,
     Browser STRING,
     BrowserVersion STRING
     ) 
     ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
     WITH SERDEPROPERTIES (
     "input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]([^\;]+).*\%20([^\/]+)[\/](.*)$"
     ) LOCATION 's3://athena-examples-myregion/cloudfront/plaintext/';
   ```
**Note**  
Replace *myregion* in `s3://athena-examples-myregion/path/to/data/` with the region identifier where you run Athena, for example, `s3://athena-examples-us-west-1/path/to/data/`\.

1. Choose **Run Query**\.

   The table `cloudfront_logs` is created and appears under the list of **Tables** for the `mydatabase` database\.  
![\[Table created in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-cf-logs-table-created.png)

## Step 3: Query Data<a name="step-3-query-data"></a>

Now that you have the `cloudfront_logs` table created in Athena based on the data in Amazon S3, you can run SQL queries on the table and see the results in Athena\. For more information about using SQL in Athena, see [SQL Reference for Amazon Athena](ddl-sql-reference.md)\.

**To run a query**

1. Open a new query tab and enter the following SQL statement in the query pane\.

   ```
   SELECT os, COUNT(*) count
   FROM cloudfront_logs
   WHERE date BETWEEN date '2014-07-05' AND date '2014-08-05'
   GROUP BY os;
   ```

1. Choose **Run Query**\.

   The results look like the following:  
![\[Viewing query results in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/results.png)

1. You can save the results of the query to a `.csv` file by choosing the download icon on the **Results** pane\.  
![\[Downloading query results in CSV format.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-query-results-download-csv.png)

1. Choose the **History** tab to view your previous queries\.  
![\[Choose History to view previous queries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-history.png)

1. Choose **Download results** to download the results of a previous query\. Query history is retained for 45 days\.  
![\[Viewing query history in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-history-tab.png)

   For more information, see [Working with Query Results, Output Files, and Query History](querying.md)\.

## Connecting to Other Data Sources<a name="getting-started-other-data-sources"></a>

This tutorial used a data source in Amazon S3 in CSV format\. You can connect Athena to a variety of data sources by using AWS Glue, ODBC and JDBC drivers, external Hive metastores, and Athena data source connectors\. For more information, see [Connecting to Data Sources](work-with-data-stores.md)\.