# Query Results<a name="querying"></a>

Athena stores query results in Amazon S3\.

Each query that you run has:
+ A results file stored automatically in a CSV format \(\*\.csv\), and
+ An Athena metadata file \(`*.csv.metadata`\)\.
**Note**  
You can delete metadata files \(`*.csv.metadata`\) without causing errors, but important information about the query is lost\.

If necessary, you can access the result files to work with them\.

You can specify the query results location in two ways: 
+ **For individual queries**\. To view or change the query results location for *individual* queries, choose **Settings** in the upper right pane of the Athena console\. This way of specifying the settings is known as *client\-side query settings*\. The client\-side settings include query results location and encryption configuration\.
+ **For all queries in a workgroup**\. To view or change the query results location for all queries in a workgroup, choose the **Workgroup:<workgroup\_name>** tab in the console, switch to your workgroup, and view or edit the workgroup\. For more information, see [Workgroup Settings](workgroups-settings.md) and [Managing Workgroups](workgroups-create-update-delete.md)\. This way of specifying the settings is known as *workgroup settings*\. The workgroup settings include the results location and encryption configuration\.

**Important**  
The client\-side query settings are used *only* if your workgroup's settings, which include query results location and encryption, is not enforced\. If your workgroup's settings override client\-side settings, then the location and encryption configuration you specify in the **Settings** are not used\. This applies to queries you run in the console, by using the API operations, or the driver\. If **Override client\-side settings** is selected, your query uses the workgroup's settings, even though the settings specified for this particular query may differ from the workgroup's settings\. For more information, see [Workgroup Settings Override Client\-Side Settings](workgroups-settings-override.md)\.  
If **Override client\-side settings** is selected for the workgroup, the following screen displays when you choose **Settings**\. This indicates that client\-side settings are not used for queries in this workgroup\.  

![\[The screenshot that shows client-side settings are overridden by workgroup's settings.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-client-side-settings-overridden.png)

## Saving Query Results<a name="saving-query-results"></a>

After you run the query, the results appear in the **Results** pane\.

To save the results of the most recent query to CSV, choose the file icon\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/savecsv.png)

To save the results of a query you ran previously, choose **History**, locate your query, and use **Download Results**\.

## The Default Location for Query Results<a name="default-location-query-results"></a>

**Important**  
The default location for query results is used *only* if you have not set the results location for queries in the workgroup, and if the workgroup settings do not override client\-side settings\. For more information, see [Workgroup Settings Override Client\-Side Settings](workgroups-settings-override.md)\.

 In this case only, Athena stores individual query results in this Amazon S3 bucket by default:

```
aws-athena-query-results-<ACCOUNTID>-<REGION>
```

Query results are saved based on the name of the query and the date the query ran, as follows:

```
{QueryLocation}/{QueryName|Unsaved}/{yyyy}/{mm}/{dd}/{QueryID}.csv
```

```
{QueryLocation}/{QueryName|Unsaved}/{yyyy}/{mm}/{dd}/{QueryID}.csv.metadata
```

In this notation:
+  `QueryLocation` is the base location for all query results if the workgroup's settings are not used\. To view or change this location, choose **Settings** in the upper right pane\. You can enter a new value for **Query result location** at any time\. You can also choose to encrypt individual query results in Amazon S3\. For more information, see [Configuring Encryption Options](encryption.md)\.
+  `QueryName` is the name of the query for which the results are saved\. If the query wasn't saved, `Unsaved` appears\. To see a list of queries and examine their SQL statements, choose **Saved queries**\.
+  `yyyy/mm/dd/` is the date the query ran\.
+  `QueryID` is the unique ID of the query\.