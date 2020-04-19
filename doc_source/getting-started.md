# Getting Started<a name="getting-started"></a>

This tutorial walks you through using Amazon Athena to query data\. You'll create a table based on sample data stored in Amazon Simple Storage Service, query the table, and check the results of the query\.

The tutorial is using live resources, so you are charged for the queries that you run\. You aren't charged for the sample data in the location that this tutorial uses, but if you upload your own data files to Amazon S3, charges do apply\.

## Prerequisites<a name="prerequisites"></a>
+ If you have not already done so, sign up for an account in [Setting Up](setting-up.md)\.
+ [Create a bucket in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) to hold your query results from Athena\.

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

Now that you have a database, you're ready to run a statement to create a table\. The table will be based on Athena sample data in the location `s3://athena-examples-aws-region/cloudfront/plaintext/`\. The statement that creates the table defines columns that map to the data, specifies how the data is delimited, and specifies the Amazon S3 location that contains the sample data\.

**To create a table**

1. For **Database**, choose `mydatabase`\.

1. Choose the plus \(**\+**\) sign in the Query Editor to create a tab with a new query\. You can have up to ten query tabs open at once\.  
![\[Choose the plus icon to create a new query.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-new-query-tab.png)

1. In the query pane, enter the following `CREATE TABLE` statement\. In the `LOCATION` statement at the end of the query, replace *myregion* with the AWS Region that you are currently using \(for example, `us-west-1`\)\. 

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
     ) ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
     WITH SERDEPROPERTIES (
     "input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]([^\;]+).*\%20([^\/]+)[\/](.*)$"
     ) LOCATION 's3://athena-examples-myregion/cloudfront/plaintext/';
   ```
**Note**  
You can query data in regions other than the region where you run Athena\. Standard inter\-region data transfer rates for Amazon S3 apply in addition to standard Athena charges\. To reduce data transfer charges, replace *myregion* in `s3://athena-examples-myregion/path/to/data/` with the region identifier where you run Athena, for example, `s3://athena-examples-us-west-1/path/to/data/`\.

1. Choose **Run Query**\.

   The table `cloudfront_logs` is created and appears under the list of **Tables** for the `mydatabase` database\.  
![\[Table created in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-cf-logs-table-created.png)

## Step 3: Query Data<a name="step-3-query-data"></a>

Now that you have the `cloudfront_logs` table created in Athena based on the data in Amazon S3, you can run queries on the table and see the results in Athena\.

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