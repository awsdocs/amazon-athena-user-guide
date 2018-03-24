# Query Results<a name="querying"></a>

Athena stores query results in Amazon S3\.

Each query that you run has:

+ A results file stored automatically in a CSV format \(\*\.csv\), and

+ A metadata file \(`*.csv.metadata`\) that includes header information, such as column type\.

If necessary, you can access the result files to work with them\. Athena stores query results in this Amazon S3 bucket by default: `aws-athena-query-results-<ACCOUNTID>-<REGION>`\.

To view or change the default location for saving query results, choose **Settings** in the upper right pane\.

**Note**  
You can delete metadata files \(`*.csv.metadata`\) without causing errors, but important information about the query is lost\.

Query results are saved in an Amazon S3 location based on the name of the query and the date the query ran, as follows:

 `{QueryLocation}/{QueryName|Unsaved}/{yyyy}/{mm}/{dd}/{QueryID}/` 

In this notation:

+  `QueryLocation` is the base location for all query results\. To view or change this location, choose **Settings**\. You can enter a new value for **Query result location** at any time\. You can also choose to encrypt query results in Amazon S3\. For more information, see [Configuring Encryption Options](encryption.md)\.

+  `QueryName` is the name of the query for which the results are saved\. If the query wasn't saved, `Unsaved` appears\. To see a list of queries and examine their SQL statements, choose **Saved queries**\.

+  `yyyy/mm/dd/` is the date the query ran\.

+  `QueryID` is the unique ID of the query\.

## Saving Query Results<a name="saving-query-results"></a>

After you run the query, the results appear in the **Results** pane\.

To save the results of the most recent query to CSV, choose the file icon\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/savecsv.png)

To save the results of a query you ran previously, choose **History**, locate your query, and use **Download Results**\.
