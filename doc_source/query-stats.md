# Viewing statistics and execution details for completed queries<a name="query-stats"></a>

After you run a query, you can get statistics on the input and output data processed, see a graphical representation of the time taken for each phase of the query, and interactively explore execution details\.

**To view query statistics for a completed query**

1. After you run a query in the Athena query editor, choose the **Query stats** tab\.  
![\[Choose Query stats.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-stats-1.png)

   The **Query stats** tab provides the following information:
   + **Data processed** – Shows you the number of input rows and bytes processed, and the number of rows and bytes output\.
   + **The Total runtime** – Shows the total amount of time the query took to run and a graphical representation of how much of that time was spent on queuing, planning, execution, and service processing\.

1. To interactively explore information about how the query ran, choose **Execution details**\.  
![\[Choose Execution details.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-stats-2.png)

   The **Execution details** page shows the execution ID for the query and a graph of the zero\-based stages in the query\. The stages are ordered start to finish from bottom to top\. Each stage's label shows the amount of time the stage took to run\.  
![\[The execution details page.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-stats-3.png)

   To navigate the graph, use the following options:
   + To zoom in or out, scroll the mouse, or use the magnifying icons\.
   + To adjust the graph to fit the screen, choose the **Zoom to fit** icon\.
   + To move the graph around, drag the mouse pointer\.

1. To see more details for a stage, choose the stage\. The stage details pane on the right shows the number of rows and bytes input and output, and an operator tree\.  
![\[Stage details pane.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-stats-4.png)

1. To see the stage details full width, choose the expand icon at the top right of the details pane\.

1. To get information about the parts of the stage, expand one or more items in the operator tree\.  
![\[Expanded operator tree.\]](http://docs.aws.amazon.com/athena/latest/ug/images/query-stats-5.png)

For more information about execution details, see [Understanding Athena EXPLAIN statement results](athena-explain-statement-understanding.md)\.

## See also<a name="query-stats-see-also"></a>

For more information, see the following resources\.

[Viewing execution plans for SQL queries](query-plans.md)

[Using EXPLAIN and EXPLAIN ANALYZE in Athena](athena-explain-statement.md)

[Optimize Amazon Athena Queries with New Query Analysis Tools](https://www.youtube.com/watch?v=7JUyTqglmNU) \(AWS YouTube channel video\)