# Working with Query Results, Output Files, and Query History<a name="querying"></a>

Amazon Athena automatically stores query results and metadata information for each query that runs in a *query result location* that you can specify in Amazon S3\. If necessary, you can access the files in this location to work with them\. You can also download query result files directly from the Athena console\.

Output files are saved automatically for every query that runs regardless of whether the query itself was saved or not\. To access and view query output files, IAM principals \(users and roles\) need permission to the Amazon S3 [GetObject](https://docs.aws.amazon.com/) action for the query result location, as well as permission for the Athena [GetQueryResults](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryResults.html) action\. The query result location can be encrypted\. If the location is encrypted, users must have the appropriate key permissions to encrypt and decrypt the query result location\.

**Important**  
IAM principals with permission to the Amazon S3 `GetObject` action for the query result location are able to retrieve query results from Amazon S3 even if permission to the Athena `GetQueryResults` action is denied\.

## Getting a Query ID<a name="get-query-id"></a>

Each query that runs is known as a *query execution*\. The query execution has a unique identifier known as the query ID or query execution ID\. To work with query result files, and to quickly find query result files, you need the query ID\. We refer to the query ID in this topic as *QueryID*\.

**To use the Athena console to get the *QueryID* of a query that ran**

1. Choose **History** from the navigation bar\.

1. From the list of queries, choose the query status under **State**—for example, **Failed**\.

1. Choose the icon next to **Query ID** to copy the ID to the clipboard\.

## Identifying Query Output Files<a name="querying-identifying-output-files"></a>

Files are saved to the query result location in Amazon S3 based on the name of the query, the query ID, and the date that the query ran\. Files for each query are named using the *QueryID*, which is a unique identifier that Athena assigns to each query when it runs\.

The following file types are saved:


| File type | File naming pattern | Description | 
| --- | --- | --- | 
|  **Query results files**  |  `QueryID.csv` or `QueryID.txt`   |  DML query results are saved in comma\-separated values \(CSV\) format\. They contain the tabular result of each query\. DDL query results are saved as plain text files\. You can download these files from the console from the **Results** pane when using the console or from the query **History**\. For more information, see [Downloading Query Results Files Using the Athena Console](#saving-query-results)\.  | 
|  **Query metadata files**  |  `QueryID.csv.metadata` or `QueryID.txt.metadata`  |  DML and DDL query metadata files are saved in binary format and are not human readable\. The file extension corresponds to the result file\. Athena uses the metadata when reading query results using the `GetQueryResults` action\. Although these files can be deleted, we do not recommend it because important information about the query is lost\.  | 
|  **Data manifest files**  |  `QueryID-manifest.csv`  |  Data manifest files are generated to track files that Athena creates in Amazon S3 data source locations when an [INSERT INTO](insert-into.md) query runs\. If a query fails, the manifest also tracks files that the query intended to write\. The manifest is useful for identifying orphaned files resulting from a failed query\.  | 

Query output files are stored in sub\-folders according to the following pattern\.

```
QueryResultsLocationInS3/[QueryName|Unsaved/yyyy/mm/dd/]
```
+ *QueryResultsLocationInS3* is the query result location specified either by workgroup settings or client\-side settings\. See [Specifying a Query Result Location](#query-results-specify-location) below\.
+ The following sub\-folders are created only for queries that run from the console\. Queries that run from the AWS CLI or using the Athena API are saved directly to the *QueryResultsLocationInS3*\.
  + *QueryName* is the name of the query for which the results are saved\. If the query ran but wasn't saved, `Unsaved` is used\. 
  + *yyyy/mm/dd* is the date that the query ran\.

Files associated with a `CREATE TABLE AS SELECT` query are stored in a `tables` sub\-folder of the above pattern\.

**To identify the query output location and query result files using the AWS CLI**
+ Use the `aws athena get-query-execution` command as shown in the following example\. Replace *abc1234d\-5efg\-67hi\-jklm\-89n0op12qr34* with the query ID\.

  ```
  aws athena get-query-execution --query-execution-id abc1234d-5efg-67hi-jklm-89n0op12qr34
  ```

  The command returns output similar to the following\. For descriptions of each output parameter, see [get\-query\-execution](https://docs.aws.amazon.com/cli/latest/reference/athena/get-query-execution.html) in the *AWS CLI Command Reference*\.

  ```
  {
      "QueryExecution": {
          "Status": {
              "SubmissionDateTime": 1565649050.175,
              "State": "SUCCEEDED",
              "CompletionDateTime": 1565649056.6229999
          },
          "Statistics": {
              "DataScannedInBytes": 5944497,
              "DataManifestLocation": "s3://aws-athena-query-results-123456789012-us-west-1/MyInsertQuery/2019/08/12/abc1234d-5efg-67hi-jklm-89n0op12qr34-manifest.csv",
              "EngineExecutionTimeInMillis": 5209
          },
          "ResultConfiguration": {
              "EncryptionConfiguration": {
                  "EncryptionOption": "SSE_S3"
              },
              "OutputLocation": "s3://aws-athena-query-results-123456789012-us-west-1/MyInsertQuery/2019/08/12/abc1234d-5efg-67hi-jklm-89n0op12qr34"
          },
          "QueryExecutionId": "abc1234d-5efg-67hi-jklm-89n0op12qr34",
          "QueryExecutionContext": {},
          "Query": "INSERT INTO mydb.elb_log_backup SELECT * FROM mydb.elb_logs LIMIT 100",
          "StatementType": "DML",
          "WorkGroup": "primary"
      }
  }
  ```

## Downloading Query Results Files Using the Athena Console<a name="saving-query-results"></a>

You can download the query results CSV file from the query pane immediately after you run a query, or using the query **History**\.

**To download the query results file of the most recent query**

1. Enter your query in the query editor and then choose **Run query**\.

   When the query finishes running, the **Results** pane shows the query results\.

1. To download the query results file, choose the file icon in the query results pane\. Depending on your browser and browser configuration, you may need to confirm the download\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/savecsv.png)

**To download a query results file for an earlier query**

1. Choose **History**\.

1. Page through the list of queries until you find the query, and then choose **Download results** under **Action** for that query\.

## Specifying a Query Result Location<a name="query-results-specify-location"></a>

The query result location that Athena uses is determined by a combination of workgroup settings and *client\-side settings*\. Client\-side settings are based on how you run the query\. 
+  If you run the query using the Athena console, the **Query result location** entered under **Settings** in the navigation bar determines the client\-side setting\. 
+ If you run the query using the Athena API, the `OutputLocation` parameter of the [StartQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_StartQueryExecution.html) action determines the client\-side setting\. 
+ If you use the ODBC or JDBC drivers to run queries, the `S3OutputLocation` property specified in the connection URL determines the client\-side setting\. 

**Important**  
When you run a query using the API or using the ODBC or JDBC driver, the console setting does not apply\. 

Each workgroup configuration has an **Override client\-side settings** option that can be enabled\. When this option is enabled, the workgroup settings take precedence over the applicable client\-side settings when an IAM principal associated with that workgroup runs the query\.

### Specifying a Query Result Location Using the Athena Console<a name="query-results-specify-location-console"></a>

Before you can run a query, a query result bucket location in Amazon S3 must be specified, or you must use a workgroup that has specified a bucket and whose configuration overrides client settings\. If no query results location is specified, the query fails with an error\.

Previously, if you ran a query without specifying a value for **Query result location**, and the query result location setting was not overridden by a workgroup, Athena created a default location for you\. The default location was `aws-athena-query-results-MyAcctID-MyRegion`, where *MyAcctID* was the AWS account ID of the IAM principal that ran the query, and *MyRegion* was the region where the query ran \(for example, `us-west-1`\.\)

Now, before you can run an Athena query in a region in which your account hasn't used Athena previously, you must specify a query result location, or use a workgroup that overrides the query result location setting\. While Athena no longer creates a default query results location for you, previously created default `aws-athena-query-results-MyAcctID-MyRegion` locations remain valid and you can continue to use them\.

**To specify the query result location using the Athena console**

1. From the nagivation bar, choose **Settings**\.

1. Enter a **Query result location**\. The location you enter is used for subsequent queries unless you change it later\.  
![\[Specifying a query result location in the Athena console\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-results-specify-location-console-1.png)

   If you are a member of a workgroup that specifies a query result location and overrides client\-side settings, the option to change the query result location is unavailable, as the following image shows:  
![\[The query result location setting is unavailable when a workgroup overrides client-side settings.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-client-side-settings-overridden.png)

### Specifying a Query Result Location Using a Workgroup<a name="query-results-specify-location-workgroup"></a>

You specify the query result location in a workgroup configuration using the AWS Management Console, the AWS CLI, or the Athena API\.

When using the AWS CLI, specify the query result location using the `OutputLocation` parameter of the `--configuration` option when you run the [aws athena create\-work\-group](https://docs.aws.amazon.com/cli/latest/reference/athena/create-work-group.html) or [aws athena update\-work\-group](https://docs.aws.amazon.com/cli/latest/reference/athena/update-work-group.html) command\.

**To specify the query result location for a workgroup using the Athena console**

1. Choose **Workgroup:*CurrentWorkgroupName*** in the navigation bar\.

1. Do one of the following:
   + If editing an existing workgroup, select it from the list, choose **View details**, and then choose **Edit Workgroup**\.
   + If creating a new workgroup, choose **Create workgroup**\.

1. For **Query result location**, choose the **Select** folder\.

1. From the list of S3 locations, choose the blue arrow successively until the bucket and folder you want to use appears in the top line\. Choose **Select**\.

1. Under **Settings**, do one of the following:
   + Select **Override client\-side settings** to save query files in the location that you specified above for all queries that members of this workgroup run\.
   + Clear **Override client\-side settings** to save query files in the location that you specfied above have the query location that you specified above only when workgroup members run queries using the Athena API, ODBC driver, or JDBC driver without specifying an output location in Amazon S3\.

1. If editing a workgroup, choose **Save**\. If creating a workgroup, choose **Create workgroup**\.

## Viewing Query History<a name="queries-viewing-history"></a>

You can use the Athena console to see the queries that succeeded and failed, download query result files for the queries that succeeded, and view error details for the queries that failed\. Athena keeps a query history for 45 days\. 

**To view query history in the Athena console**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose the **History** tab\. The **History** tab shows information about each query that ran\.  
![\[Viewing the query history in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-history.png)

1. Do one of the following:
   + To see a query statement in the Query Editor, choose the text of the query in the **Query** column\. Longer query statements are abbreviated\.  
![\[Choose the text of a query to see it in the Query Editor.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-history-view-query-statement.png)
   + To see a query ID, chose its **State** \(**Succeeded**, **Failed**, or **Cancelled**\)\. The query ID shows in a pointer tip\.  
![\[Viewing a query ID on the History tab of the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-history-viewing-query-id.png)
   + To download the results of a successful query into a `.csv` file, choose **Download results**\.  
![\[Downloading query results from the History tab of the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-history-download-results.png)
   + To see the details for a query that failed, choose **Error details** for the query\.  
![\[Choose Error details to view the details of a failed query.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-history-choose-error-details.png)  
![\[Viewing the error details of a query that failed.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-history-error-details.png)

If you want to keep the query history longer than 45 days, you can retrieve the query history and save it to a data store such as Amazon S3\. To automate this process, you can use Athena and Amazon S3 API actions and CLI commands\. The following procedure summarizes these steps\.

**To retrieve and save query history programmatically**

1. Use Athena [ListQueryExecutions](https://docs.aws.amazon.com/athena/latest/APIReference/API_ListQueryExecutions.html) API action or the [list\-query\-executions](https://docs.aws.amazon.com/cli/latest/reference/athena/list-query-executions.html) CLI command to retrieve the query IDs\.

1. Use the Athena [GetQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryExecution.html) API action or the [get\-query\-execution](https://docs.aws.amazon.com/cli/latest/reference/athena/get-query-execution.html) CLI command to retrieve information about each query based on its ID\.

1. Use the Amazon S3 [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) API action or the [put\-object](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html) CLI command to save the information in Amazon S3\.
