# Supported Actions for Views in Athena<a name="views-supported-actions"></a>

Athena supports the following actions for views\. You can run these commands in the Query Editor\.


| Statement | Description | 
| --- | --- | 
| [CREATE VIEW](create-view.md) |  Creates a new view from a specified `SELECT` query\. For more information, see [Creating Views](creating-views.md)\. The optional `OR REPLACE` clause lets you update the existing view by replacing it\.  | 
| [DESCRIBE VIEW](describe-view.md) |  Shows the list of columns for the named view\. This allows you to examine the attributes of a complex view\.   | 
| [DROP VIEW](drop-view.md) |  Deletes an existing view\. The optional `IF EXISTS` clause suppresses the error if the view does not exist\. For more information, see [Deleting Views](deleting-views.md)\.  | 
| [SHOW CREATE VIEW](show-create-view.md) |  Shows the SQL statement that creates the specified view\.  | 
| [SHOW VIEWS](show-views.md) |  Lists the views in the specified database, or in the current database if you omit the database name\. Use the optional `LIKE` clause with a regular expression to restrict the list of view names\. You can also see the list of views in the left pane in the console\.  | 
| [SHOW COLUMNS](show-columns.md) |  Lists the columns in the schema for a view\.  | 

## Considerations for Views<a name="considerations-views"></a>

In Athena, you can preview and work with views created in the Athena Console, in the AWS Glue Data Catalog, if you have migrated to using it, or with Presto running on the Amazon EMR cluster connected to the same catalog\. You cannot preview or add to Athena views that were created in other ways\.

 Athena prevents you from running recursive views and displays an error message in such cases\. A recursive view is a view query that references itself\.

Athena detects stale views and displays an error message in such cases\. A stale view is a view query that references tables or databases that do not exist\.

You can create and run nested views as long as the query behind the nested view is valid and the tables and databases exist\.

Athena view names cannot contain special characters, other than underscore `(_)`\. For more information, see [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)\.

Avoid using reserved keywords for naming views\. If you use reserved keywords, use double quotes to enclose reserved keywords in your queries on views\. See [Reserved Keywords](reserved-words.md)\.

You cannot use views with geospatial functions\.

You cannot use views to manage access control on data in Amazon S3\. To query a view, you need permissions to access the data stored in Amazon S3\. For more information, see [Access to Amazon S3](s3-permissions.md)\.