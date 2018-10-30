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