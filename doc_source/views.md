# Working with Views<a name="views"></a>

A view in Amazon Athena is a logical, not a physical table\. The query that defines a view runs each time the view is referenced in a query\.

You can create a view from a `SELECT` query and then reference this view in future queries\. For more information, see [CREATE VIEW](create-view.md)\.

**Topics**
+ [When to Use Views?](#when-to-use-views)
+ [Supported Actions for Views in Athena](#views-supported-actions)
+ [Considerations for Views](#considerations-views)
+ [Limitations for Views](#limitations-views)
+ [Working with Views in the Console](#views-console)
+ [Creating Views](#creating-views)
+ [Examples of Views](#views-examples)
+ [Updating Views](#updating-views)
+ [Deleting Views](#deleting-views)

## When to Use Views?<a name="when-to-use-views"></a>

You may want to create views to: 
+ *Query a subset of data*\. For example, you can create a view with a subset of columns from the original table to simplify querying data\. 
+ *Combine multiple tables in one query*\. When you have multiple tables and want to combine them with `UNION ALL`, you can create a view with that expression to simplify queries against the combined tables\.
+ *Hide the complexity of existing base queries and simplify queries run by users*\. Base queries often include joins between tables, expressions in the column list, and other SQL syntax that make it difficult to understand and debug them\. You might create a view that hides the complexity and simplifies queries\.
+ *Experiment with optimization techniques and create optimized queries*\. For example, if you find a combination of `WHERE` conditions, `JOIN` order, or other expressions that demonstrate the best performance, you can create a view with these clauses and expressions\. Applications can then make relatively simple queries against this view\. If you later find a better way to optimize the original query, when you recreate the view, all the applications immediately take advantage of the optimized base query\. 
+ *Hide the underlying table and column names, and minimize maintenance problems* if those names change\. In that case, you recreate the view using the new names\. All queries that use the view rather than the underlying tables keep running with no changes\.

## Supported Actions for Views in Athena<a name="views-supported-actions"></a>

Athena supports the following actions for views\. You can run these commands in the Query Editor\.


| Statement | Description | 
| --- | --- | 
| [CREATE VIEW](create-view.md) |  Creates a new view from a specified `SELECT` query\. For more information, see [Creating Views](#creating-views)\. The optional `OR REPLACE` clause lets you update the existing view by replacing it\.  | 
| [DESCRIBE VIEW](describe-view.md) |  Shows the list of columns for the named view\. This allows you to examine the attributes of a complex view\.   | 
| [DROP VIEW](drop-view.md) |  Deletes an existing view\. The optional `IF EXISTS` clause suppresses the error if the view does not exist\. For more information, see [Deleting Views](#deleting-views)\.  | 
| [SHOW CREATE VIEW](show-create-view.md) |  Shows the SQL statement that creates the specified view\.  | 
| [SHOW VIEWS](show-views.md) |  Lists the views in the specified database, or in the current database if you omit the database name\. Use the optional `LIKE` clause with a regular expression to restrict the list of view names\. You can also see the list of views in the left pane in the console\.  | 
| [SHOW COLUMNS](show-columns.md) |  Lists the columns in the schema for a view\.  | 

## Considerations for Views<a name="considerations-views"></a>

The following considerations apply to creating and using views in Athena:
+ In Athena, you can preview and work with views created in the Athena Console, in the AWS Glue Data Catalog, if you have migrated to using it, or with Presto running on the Amazon EMR cluster connected to the same catalog\. You cannot preview or add to Athena views that were created in other ways\.
+  If you are creating views through the AWS GlueData Catalog, you must include the `PartitionKeys` parameter and set its value to an empty list, as follows: `"PartitionKeys":[]`\. Otherwise, your view query will fail in Athena\. The following example shows a view created from the Data Catalog with `"PartitionKeys":[]`:

  ```
  aws glue create-table 
  --database-name mydb 
  --table-input '{
  "Name":"test",
    "TableType": "EXTERNAL_TABLE",  
    "Owner": "hadoop",  
    "StorageDescriptor":{
       "Columns":[{
             "Name":"a","Type":"string"},{"Name":"b","Type":"string"}],
     "Location":"s3://xxxxx/Oct2018/25Oct2018/",
     "InputFormat":"org.apache.hadoop.mapred.TextInputFormat", 
     "OutputFormat": "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat", 
     "SerdeInfo":{"SerializationLibrary":"org.apache.hadoop.hive.serde2.OpenCSVSerde",
     "Parameters":{"separatorChar": "|", "serialization.format": "1"}}},"PartitionKeys":[]}'
  ```
+ If you have created Athena views in the Data Catalog, then Data Catalog treats views as tables\. You can use table level fine\-grained access control in Data Catalog to [restrict access](fine-grained-access-to-glue-resources.md) to these views\. 
+  Athena prevents you from running recursive views and displays an error message in such cases\. A recursive view is a view query that references itself\.
+ Athena displays an error message when it detects stale views\. A stale view is reported when one of the following occurs:
  + The view references tables or databases that do not exist\.
  + A schema or metadata change is made in a referenced table\. 
  + A referenced table is dropped and recreated with a different schema or configuration\.
+ You can create and run nested views as long as the query behind the nested view is valid and the tables and databases exist\.

## Limitations for Views<a name="limitations-views"></a>
+ Athena view names cannot contain special characters, other than underscore `(_)`\. For more information, see [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)\.
+ Avoid using reserved keywords for naming views\. If you use reserved keywords, use double quotes to enclose reserved keywords in your queries on views\. See [Reserved Keywords](reserved-words.md)\.
+ You cannot use views with federated data sources, external Hive metastores, or UDFs\.
+ You cannot use views with geospatial functions\.
+ You cannot use views to manage access control on data in Amazon S3\. To query a view, you need permissions to access the data stored in Amazon S3\. For more information, see [Access to Amazon S3](s3-permissions.md)\.

## Working with Views in the Console<a name="views-console"></a>

In the Athena console, you can:
+ Locate all views in the left pane, where tables are listed\. Athena runs a [SHOW VIEWS](show-views.md) operation to present this list to you\.
+ Filter views\.
+ Preview a view, show its properties, edit it, or delete it\.

**To list the view actions in the console**

A view shows up in the console only if you have already created it\.

1. In the Athena console, choose **Views**, choose a view, then expand it\.

   The view displays, with the columns it contains, as shown in the following example:  
![\[The screenshot of an expanded view that shows columns.\]](http://docs.aws.amazon.com/athena/latest/ug/images/view_expanded.png)

1. In the list of views, choose a view, and open the context \(right\-click\) menu\. The actions menu icon \(⋮\) is highlighted for the view that you chose, and the list of actions opens, as shown in the following example:  
![\[The screenshot of the context menu for views. It lets you preview, show properties, edit, and delete a view.\]](http://docs.aws.amazon.com/athena/latest/ug/images/view_options.png)

1. Choose an option\. For example, **Show properties** shows the view name, the name of the database in which the table for the view is created in Athena, and the time stamp when it was created:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/view_properties.png)

## Creating Views<a name="creating-views"></a>

You can create a view from any `SELECT` query\.

**To create a view in the console**

Before you create a view, choose a database and then choose a table\. Run a `SELECT` query on a table and then create a view from it\.

1. In the Athena console, choose **Create view**\.  
![\[The screenshot that shows the button for creating a view.\]](http://docs.aws.amazon.com/athena/latest/ug/images/create_view.png)

   In the Query Editor, a sample view query displays\. 

1. Edit the sample view query\. Specify the table name and add other syntax\. For more information, see [CREATE VIEW](create-view.md) and [Examples of Views](#views-examples)\. 

   View names cannot contain special characters, other than underscore `(_)`\. See [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)\. Avoid using [Reserved Keywords](reserved-words.md) for naming views\.

1. Run the view query, debug it if needed, and save it\.

Alternatively, create a query in the Query Editor, and then use **Create view from query**\. 

![\[The screenshot that shows the button for creating a view from query.\]](http://docs.aws.amazon.com/athena/latest/ug/images/create_view_from_query.png)

If you run a view that is not valid, Athena displays an error message\.

If you delete a table from which the view was created, when you attempt to run the view, Athena displays an error message\.

You can create a nested view, which is a view on top of an existing view\. Athena prevents you from running a recursive view that references itself\.

## Examples of Views<a name="views-examples"></a>

To show the syntax of the view query, use [SHOW CREATE VIEW](show-create-view.md)\.

**Example 1**  
Consider the following two tables: a table `employees` with two columns, `id` and `name`, and a table `salaries`, with two columns, `id` and `salary`\.   
In this example, we create a view named `name_salary` as a `SELECT` query that obtains a list of IDs mapped to salaries from the tables `employees` and `salaries`:  

```
CREATE VIEW name_salary AS
SELECT
 employees.name, 
 salaries.salary 
FROM employees, salaries 
WHERE employees.id = salaries.id
```

**Example 2**  
In the following example, we create a view named `view1` that enables you to hide more complex query syntax\.   
This view runs on top of two tables, `table1` and `table2`, where each table is a different `SELECT` query\. The view selects columns from `table1` and joins the results with `table2`\. The join is based on column `a` that is present in both tables\.  

```
CREATE VIEW view1 AS
WITH
  table1 AS (
         SELECT a, 
         MAX(b) AS the_max 
         FROM x 
         GROUP BY a
         ),
  table2 AS (
         SELECT a, 
         AVG(d) AS the_avg 
         FROM y 
         GROUP BY a)
SELECT table1.a, table1.the_max, table2.the_avg
FROM table1
JOIN table2 
ON table1.a = table2.a;
```

## Updating Views<a name="updating-views"></a>

After you create a view, it appears in the **Views** list in the left pane\. 

To edit the view, choose it, choose the context \(right\-click\) menu, and then choose **Show/edit query**\. You can also edit the view in the Query Editor\. For more information, see [CREATE VIEW](create-view.md)\.

## Deleting Views<a name="deleting-views"></a>

To delete a view, choose it, choose the context \(right\-click\) menu, and then choose **Delete view**\. For more information, see [DROP VIEW](drop-view.md)\.