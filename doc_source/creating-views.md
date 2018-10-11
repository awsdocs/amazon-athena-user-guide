# Creating Views<a name="creating-views"></a>

You can create a view from any `SELECT` query\.

**To create a view in the console**

Before you create a view, choose a database and then choose a table\. Run a `SELECT` query on a table and then create a view from it\.

1. In the Athena console, choose **Create view**\.  
![\[The screenshot that shows the button for creating a view.\]](http://docs.aws.amazon.com/athena/latest/ug/images/create_view.PNG)

   In the Query Editor, a sample view query displays\. 

1. Edit the sample view query\. Specify the table name and add other syntax\. For more information, see [CREATE VIEW](create-view.md) and [Examples of Views](views-examples.md)\. 

   View names cannot contain special characters, other than underscore `(_)`\. See [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)\. Avoid using [Reserved Keywords](reserved-words.md) for naming views\.

1. Run the view query, debug it if needed, and save it\.

Alternatively, create a query in the Query Editor, and then use **Create view from query**\. 

![\[The screenshot that shows the button for creating a view from query.\]](http://docs.aws.amazon.com/athena/latest/ug/images/create_view_from_query.PNG)

If you run a view that is not valid, Athena displays an error message\.

If you delete a table from which the view was created, when you attempt to run the view, Athena displays an error message\.

You can create a nested view, which is a view on top of an existing view\. Athena prevents you from running a recursive view that references itself\.