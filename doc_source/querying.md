# Working with query results, recent queries, and output files<a name="querying"></a>

Amazon Athena automatically stores query results and metadata information for each query that runs in a *query result location* that you can specify in Amazon S3\. If necessary, you can access the files in this location to work with them\. You can also download query result files directly from the Athena console\.

To set up an Amazon S3 query result location for the first time, see [Specifying a query result location using the Athena console](#query-results-specify-location-console)\.

Output files are saved automatically for every query that runs\. To access and view query output files, IAM principals \(users and roles\) need permission to the Amazon S3 [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) action for the query result location, as well as permission for the Athena [GetQueryResults](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryResults.html) action\. The query result location can be encrypted\. If the location is encrypted, users must have the appropriate key permissions to encrypt and decrypt the query result location\.

**Important**  
IAM principals with permission to the Amazon S3 `GetObject` action for the query result location are able to retrieve query results from Amazon S3 even if permission to the Athena `GetQueryResults` action is denied\.

## Specifying a query result location<a name="query-results-specify-location"></a>

The query result location that Athena uses is determined by a combination of workgroup settings and *client\-side settings*\. Client\-side settings are based on how you run the query\. 
+  If you run the query using the Athena console, the **Query result location** entered under **Settings** in the navigation bar determines the client\-side setting\. 
+ If you run the query using the Athena API, the `OutputLocation` parameter of the [StartQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_StartQueryExecution.html) action determines the client\-side setting\. 
+ If you use the ODBC or JDBC drivers to run queries, the `S3OutputLocation` property specified in the connection URL determines the client\-side setting\. 

**Important**  
When you run a query using the API or using the ODBC or JDBC driver, the console setting does not apply\. 

Each workgroup configuration has an **Override client\-side settings** option that can be enabled\. When this option is enabled, the workgroup settings take precedence over the applicable client\-side settings when an IAM principal associated with that workgroup runs the query\.

### Specifying a query result location using the Athena console<a name="query-results-specify-location-console"></a>

Before you can run a query, a query result bucket location in Amazon S3 must be specified, or you must use a workgroup that has specified a bucket and whose configuration overrides client settings\.

**To specify a client\-side setting query result location using the Athena console**

1. [Switch](workgroups-create-update-delete.md#switching-workgroups) to the workgroup for which you want to specify a query results location\. The name of the default workgroup is **primary**\.

1. From the navigation bar, choose **Settings**\.

1. From the navigation bar, choose **Manage**\.

1. For **Manage settings**, do one of the following:
   + In the **Location of query result** box, enter the path to the bucket that you created in Amazon S3 for your query results\. Prefix the path with `s3://`\.
   + Choose **Browse S3**, choose the Amazon S3 bucket that you created for your current Region, and then choose **Choose**\.
**Note**  
If you are using a workgroup that specifies a query result location for all users of the workgroup, the option to change the query result location is unavailable\.

1. \(Optional\) For **Expected bucket owner**, enter the ID of the AWS account that you expect to be the owner of the output location bucket\. This is an added security measure\. If the account ID of the bucket owner does not match the ID that you specify here, attempts to output to the bucket will fail\. For in\-depth information, see [Verifying bucket ownership with bucket owner condition](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-owner-condition.html) in the *Amazon S3 User Guide*\.
**Note**  
The expected bucket owner setting applies only to the Amazon S3 output location that you specify for Athena query results\. It does not apply to other Amazon S3 locations like data source locations in external Amazon S3 buckets, `CTAS` and `INSERT INTO` destination table locations, `UNLOAD` statement output locations, operations to spill buckets for federated queries, or `SELECT` queries run against a table in another account\.

1. \(Optional\) Choose **Encrypt query results** if you want to encrypt the query results stored in Amazon S3\. For more information about encryption in Athena, see [Encryption at rest](encryption.md)\.

1. \(Optional\) Choose **Assign bucket owner full control over query results** to grant full control access over query results to the bucket owner when [ACLs are enabled](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) for the query result bucket\. For example, if your query result location is owned by another account, you can grant ownership and full control over your query results to the other account\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) in the *Amazon S3 User Guide*\.

1. Choose **Save**\.

#### Previously created default locations<a name="query-results-specify-location-previous-defaults"></a>

Previously in Athena, if you ran a query without specifying a value for **Query result location**, and the query result location setting was not overridden by a workgroup, Athena created a default location for you\. The default location was `aws-athena-query-results-MyAcctID-MyRegion`, where *MyAcctID* was the Amazon Web Services account ID of the IAM principal that ran the query, and *MyRegion* was the region where the query ran \(for example, `us-west-1`\.\)

Now, before you can run an Athena query in a region in which your account hasn't used Athena previously, you must specify a query result location, or use a workgroup that overrides the query result location setting\. While Athena no longer creates a default query results location for you, previously created default `aws-athena-query-results-MyAcctID-MyRegion` locations remain valid and you can continue to use them\.

### Specifying a query result location using a workgroup<a name="query-results-specify-location-workgroup"></a>

You specify the query result location in a workgroup configuration using the AWS Management Console, the AWS CLI, or the Athena API\.

When using the AWS CLI, specify the query result location using the `OutputLocation` parameter of the `--configuration` option when you run the [https://docs.aws.amazon.com/cli/latest/reference/athena/create-work-group.html](https://docs.aws.amazon.com/cli/latest/reference/athena/create-work-group.html) or [https://docs.aws.amazon.com/cli/latest/reference/athena/update-work-group.html](https://docs.aws.amazon.com/cli/latest/reference/athena/update-work-group.html) command\.

**To specify the query result location for a workgroup using the Athena console**

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Workgroups**\.

1. In the list of workgroups, choose the link of the workgroup that you want to edit\.

1. Choose **Edit**\.

1. For **Query result location and encryption**, do one of the following:
   + In the **Location of query result** box, enter the path to a bucket in Amazon S3 for your query results\. Prefix the path with `s3://`\.
   + Choose **Browse S3**, choose the Amazon S3 bucket for your current Region that you want to use, and then choose **Choose**\.

1. \(Optional\) For **Expected bucket owner**, enter the ID of the AWS account that you expect to be the owner of the output location bucket\. This is an added security measure\. If the account ID of the bucket owner does not match the ID that you specify here, attempts to output to the bucket will fail\. For in\-depth information, see [Verifying bucket ownership with bucket owner condition](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-owner-condition.html) in the *Amazon S3 User Guide*\.
**Note**  
The expected bucket owner setting applies only to the Amazon S3 output location that you specify for Athena query results\. It does not apply to other Amazon S3 locations like data source locations in external Amazon S3 buckets, `CTAS` and `INSERT INTO` destination table locations, `UNLOAD` statement output locations, operations to spill buckets for federated queries, or `SELECT` queries run against a table in another account\.

1. \(Optional\) Choose **Encrypt query results** if you want to encrypt the query results stored in Amazon S3\. For more information about encryption in Athena, see [Encryption at rest](encryption.md)\.

1. \(Optional\) Choose **Assign bucket owner full control over query results** to grant full control access over query results to the bucket owner when [ACLs are enabled](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) for the query result bucket\. For example, if your query result location is owned by another account, you can grant ownership and full control over your query results to the other account\. 

   If the bucket's S3 Object Ownership setting is **Bucket owner preferred**, the bucket owner also owns all query result objects written from this workgroup\. For example, if an external account's workgroup enables this option and sets its query result location to your account's Amazon S3 bucket which has an S3 Object Ownership setting of **Bucket owner preferred**, you own and have full control access over the external workgroup's query results\. 

   Selecting this option when the query result bucket's S3 Object Ownership setting is **Bucket owner enforced** has no effect\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) in the *Amazon S3 User Guide*\. 

1. If you want to require all users of the workgroup to use the query results location that you specified, scroll down to the **Settings** section and select **Override client\-side settings**\.

1. Choose **Save changes**\.

## Downloading query results files using the Athena console<a name="saving-query-results"></a>

You can download the query results CSV file from the query pane immediately after you run a query\. You can also download query results from recent queries from the **Recent queries** tab\.

**Note**  
Athena query result files are data files that contain information that can be configured by individual users\. Some programs that read and analyze this data can potentially interpret some of the data as commands \(CSV injection\)\. For this reason, when you import query results CSV data to a spreadsheet program, that program might warn you about security concerns\. To keep your system secure, you should always choose to disable links or macros from downloaded query results\.

**To run a query and download the query results**

1. Enter your query in the query editor and then choose **Run**\.

   When the query finishes running, the **Results** pane shows the query results\.

1. To download a CSV file of the query results, choose **Download results** above the query results pane\. Depending on your browser and browser configuration, you may need to confirm the download\.  
![\[Saving query results to a .csv file in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-query-results-download-csv.png)

**To download a query results file for an earlier query**

1. Choose **Recent queries**\.  
![\[Choose Recent queries to view previous queries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/getting-started-recent-queries.png)

1. Use the search box to find the query, select the query, and then choose **Download results**\. Queries are retained for 45 days\.  
![\[Choose Recent queries to find and download previous query results.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-recent-queries-tab-download.png)

## Viewing recent queries<a name="queries-viewing-history"></a>

You can use the Athena console to see which queries succeeded or failed, and view error details for the queries that failed\. Athena keeps a query history for 45 days\. 

**To view recent queries in the Athena console**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Recent queries**\. The **Recent queries** tab shows information about each query that ran\.

1. To open a query statement in the query editor, choose the query's execution ID\.  
![\[Choose the execution ID of a query to see it in the query editor.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-view-query-statement.png)

1. To see the details for a query that failed, choose the **Failed** link for the query\.  
![\[Choose the Failed link for a query to view information about the failure.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-view-query-failure-details.png)

### Downloading multiple recent queries to a CSV file<a name="queries-downloading-multiple-recent-queries-to-csv"></a>

You can use the **Recent queries** tab of the Athena console to export one or more recent queries to a CSV file in order to view them in tabular format\. The downloaded file contains not the query results, but the SQL query string itself and other information about the query\. Exported fields include the execution ID, query string contents, query start time, status, run time, amount of data scanned, query engine version used, and encryption method\. You can export a maximum of 500 recent queries, or a filtered maximum of 500 queries using criteria that you enter in the search box\.

**To export one or more recent queries to a CSV file**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Recent queries**\.

1. \(Optional\) Use the search box to filter for the recent queries that you want to download\.

1. Choose **Download CSV**\.  
![\[Choose Download CSV.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-recent-queries-csv.png)

1. At the file save prompt, choose **Save**\. The default file name is `Recent Queries` followed by a timestamp \(for example, `Recent Queries 2022-12-05T16 04 27.352-08 00.csv`\)

### Configuring recent query display options<a name="queries-recent-queries-configuring-options"></a>

You can configure options for the **Recent queries** tab like columns to display and text wrapping\.

**To configure options for the **Recent queries** tab**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose **Recent queries**\.

1. Choose the options button \(gear icon\)\.  
![\[Choose the option button to configure the display of recent queries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-recent-queries-options.png)

1. In the **Preferences** dialog box, choose the number of rows per page, line wrapping behavior, and columns to display\.  
![\[Configuring the display of recent queries.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-recent-queries-preferences.png)

1. Choose **Confirm**\.

### Keeping query history longer than 45 days<a name="querying-keeping-query-history"></a>

If you want to keep the query history longer than 45 days, you can retrieve the query history and save it to a data store such as Amazon S3\. To automate this process, you can use Athena and Amazon S3 API actions and CLI commands\. The following procedure summarizes these steps\.

**To retrieve and save query history programmatically**

1. Use Athena [ListQueryExecutions](https://docs.aws.amazon.com/athena/latest/APIReference/API_ListQueryExecutions.html) API action or the [list\-query\-executions](https://docs.aws.amazon.com/cli/latest/reference/athena/list-query-executions.html) CLI command to retrieve the query IDs\.

1. Use the Athena [GetQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryExecution.html) API action or the [get\-query\-execution](https://docs.aws.amazon.com/cli/latest/reference/athena/get-query-execution.html) CLI command to retrieve information about each query based on its ID\.

1. Use the Amazon S3 [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) API action or the [put\-object](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html) CLI command to save the information in Amazon S3\.

## Finding query output files in Amazon S3<a name="querying-finding-output-files"></a>

Query output files are stored in sub\-folders on Amazon S3 in the following path pattern unless the query occurs in a workgroup whose configuration overrides client\-side settings\. When workgroup configuration overrides client\-side settings, the query uses the results path specified by the workgroup\.

```
QueryResultsLocationInS3/[QueryName|Unsaved/yyyy/mm/dd/]
```
+ *QueryResultsLocationInS3* is the query result location specified either by workgroup settings or client\-side settings\. For more information, see [Specifying a query result location](#query-results-specify-location) later in this document\.
+ The following sub\-folders are created only for queries run from the console whose results path has not been overriden by workgroup configuration\. Queries that run from the AWS CLI or using the Athena API are saved directly to the *QueryResultsLocationInS3*\.
  + *QueryName* is the name of the query for which the results are saved\. If the query ran but wasn't saved, `Unsaved` is used\. 
  + *yyyy/mm/dd* is the date that the query ran\.

Files associated with a `CREATE TABLE AS SELECT` query are stored in a `tables` sub\-folder of the above pattern\.

### Identifying query output files<a name="querying-identifying-output-files"></a>

Files are saved to the query result location in Amazon S3 based on the name of the query, the query ID, and the date that the query ran\. Files for each query are named using the *QueryID*, which is a unique identifier that Athena assigns to each query when it runs\.

The following file types are saved:


| File type | File naming patterns | Description | 
| --- | --- | --- | 
|  **Query results files**  |  `QueryID.csv` `QueryID.txt`  |  DML query results files are saved in comma\-separated values \(CSV\) format\. DDL query results are saved as plain text files\.  You can download results files from the console from the **Results** pane when using the console or from the query **History**\. For more information, see [Downloading query results files using the Athena console](#saving-query-results)\.  | 
|  **Query metadata files**  |  `QueryID.csv.metadata` `QueryID.txt.metadata`  |  DML and DDL query metadata files are saved in binary format and are not human readable\. The file extension corresponds to the related query results file\. Athena uses the metadata when reading query results using the `GetQueryResults` action\. Although these files can be deleted, we do not recommend it because important information about the query is lost\.  | 
|  **Data manifest files**  |  `QueryID-manifest.csv`  |  Data manifest files are generated to track files that Athena creates in Amazon S3 data source locations when an [INSERT INTO](insert-into.md) query runs\. If a query fails, the manifest also tracks files that the query intended to write\. The manifest is useful for identifying orphaned files resulting from a failed query\.  | 

### Using the AWS CLI to identify query output location and files<a name="querying-finding-output-files"></a>

To use the AWS CLI to identify the query output location and result files, run the `aws athena get-query-execution` command, as in the following example\. Replace *abc1234d\-5efg\-67hi\-jklm\-89n0op12qr34* with the query ID\.

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