# Getting Started<a name="getting-started"></a>

This tutorial walks you through using Amazon Athena to query data\. You'll create a table based on sample data stored in Amazon Simple Storage Service, query the table, and check the results of the query\.

The tutorial is using live resources, so you are charged for the queries that you run\. You aren't charged for the sample datasets that you use, but if you upload your own data files to Amazon S3, charges do apply\.

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

1. In the Athena Query Editor, you see a query pane with an example query\. Start typing your query anywhere in the query pane\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/example_query.png)

1. To create a database named `mydatabase`, enter the following CREATE DATABASE statement, and then choose **Run Query**:

   ```
   CREATE DATABASE mydatabase
   ```

1. Confirm that the catalog display refreshes and `mydatabase` appears in the **DATABASE** list in the **Catalog** dashboard on the left side\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/db_list_and_refresh.png)

## Step 2: Create a Table<a name="step-2-create-a-table"></a>

Now that you have a database, you're ready to create a table that's based on the sample data file\. You define columns that map to the data, specify how the data is delimited, and provide the location in Amazon S3 for the file\.

**To create a table**

1. Make sure that `mydatabase` is selected for **DATABASE** and then choose **New Query**\.

1. In the query pane, enter the following CREATE TABLE statement, and then choose **Run Query**:
**Note**  
You can query data in regions other than the region where you run Athena\. Standard inter\-region data transfer rates for Amazon S3 apply in addition to standard Athena charges\. To reduce data transfer charges, replace *myregion* in `s3://athena-examples-myregion/path/to/data/` with the region identifier where you run Athena, for example, `s3://athena-examples-us-west-1/path/to/data/`\.

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

   The `table cloudfront_logs` is created and appears in the **Catalog** dashboard for your database\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/table_create.png)

## Step 3: Query Data<a name="step-3-query-data"></a>

Now that you have the `cloudfront_logs` table created in Athena based on the data in Amazon S3, you can run queries on the table and see the results in Athena\.

**To run a query**

1. Choose **New Query**, enter the following statement anywhere in the query pane, and then choose **Run Query**:

   ```
   SELECT os, COUNT(*) count
   FROM cloudfront_logs
   WHERE date BETWEEN date '2014-07-05' AND date '2014-08-05'
   GROUP BY os;
   ```

   Results are returned that look like the following:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/results.png)

1. Optionally, you can save the results of a query to CSV by choosing the file icon on the **Results** pane\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/savecsv.png)

You can also view the results of previous queries or queries that may take some time to complete\. Choose **History** then either search for your query or choose **View** or **Download** to view or download the results of previous completed queries\. This also displays the status of queries that are currently running\. Query history is retained for 45 days\. For information, see [Viewing Query History](querying.md#queries-viewing-history)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/history.png)

Query results are also stored in Amazon S3 in a bucket called aws\-athena\-query\-results\-*ACCOUNTID*\-*REGION*\. You can change the default location in the console and encryption options by choosing **Settings** in the upper right pane\. For more information, see [Query Results](querying.md)\.