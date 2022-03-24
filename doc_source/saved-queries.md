# Using saved queries<a name="saved-queries"></a>

You can use the Athena console to save, edit, run, rename, and delete the queries that you create in the query editor\.

## Considerations and limitations<a name="saved-queries-considerations-and-limitations"></a>
+ You can update the name, description, and query text of saved queries\.
+ You can only update the queries in your own account\.
+ You cannot change the workgroup or database to which the query belongs\.
+ Athena does not keep a history of query modifications\. If you want to keep a particular version of a query, save it with a different name\.

## Working with saved queries in the Athena console<a name="saved-queries--athena-console"></a>

**To save a query and give it a name**

1. In the Athena console query editor, enter or run a query\.

1. At the bottom of the query editor window, choose **Save**, **Save as**\.

1. In the **Save query** dialog box, enter a name for the query and an optional description\. You can use the expandable **Preview SQL query** window to verify the contents of the query before you save it\.

1. Choose **Save**\.

   In the query editor, the tab for the query shows the name that you specified\.

**To run a saved query**

1. In the Athena console, choose the **Saved queries** tab\.

1. In the **Saved queries** list, choose the name of the query that you want to run\.

   The query editor displays the query that you chose\.

1. Choose **Run**\.

**To rename a saved query**

1. In the Athena console, choose the **Saved queries** tab\.

1. Select the check box for the query that you want to rename\.

1. Choose **Rename**\.

1. In the **Rename query** dialog box, edit the query name and query description\. You can use the expandable **Preview SQL query** window to verify the contents of the query before you rename it\.

1. Choose **Rename query**\.

   The renamed query appears in the **Saved queries** list\.

**To edit a saved query**

1. In the Athena console, choose the **Saved queries** tab\.

1. In the **Saved queries** list, choose the name of the query that you want to edit\.

1. Edit the query in the query editor\.

1. Perform one of the following steps:
   + To run the query, choose **Run**\.
   + To save the query, choose **Save**, **Save**\.
   + To save the query with a different name, choose **Save**, **Save as**\.

**To delete a saved query**

1. In the Athena console, choose the **Saved queries** tab\.

1. Select one or more check boxes for the queries that you want to delete\.

1. Choose **Delete**\.

1. In the **Delete saved queries** prompt, choose **Delete**\.

   The queries are removed from the **Saved queries** list\.

## Using the Athena API to update saved queries<a name="saved-queries-athena-api"></a>

For information about using the Athena API to update a saved query, see the [UpdateNamedQuery](https://docs.aws.amazon.com/athena/latest/APIReference/API_UpdateNamedQuery.html) action in the Athena API Reference\.