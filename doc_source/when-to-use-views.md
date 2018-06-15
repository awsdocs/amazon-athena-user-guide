# When to Use Views?<a name="when-to-use-views"></a>

You may want to create views to: 
<<<<<<< HEAD
+ *Query a subset of data*\. For example, you can create a table with a subset of columns from the original table to simplify querying data\. 
+ *Combine multiple tables in one query*\. When you have multiple tables and want to combine them with `UNION ALL`, you can create a view with that expression to simplify queries against the combined tables\.
+ *Hide the complexity of existing base queries and simplify queries run by users*\. Base queries often include joins between tables, expressions in the column list, and other SQL syntax that make it difficult to understand and debug them\. You might create a view that hides the complexity and simplifies queries\.
=======
+ *Query a subset of data*\. For example, you can create a table with a subset of columns from the original table to simplify querying data\.
+ *Combine multiple tables in one query*\. When you have multiple tables and want to combine them with `UNION ALL`, you can create a view with that expression to simplify queries against the combined tables\.
+ *Hide the complexity of existing base queries and simplify queries run by users*\. Base queries often include joins between tables, expressions in the column list, and other SQL syntax that make it difficult to understand and debug them\. You might create a view that hides the complexity and simplifies queries\.

>>>>>>> 16a0fb48139551796630e9280aa629d83bff5441
+ *Experiment with optimization techniques and create optimized queries*\. For example, if you find a combination of `WHERE` conditions, `JOIN` order, or other expressions that demonstrate the best performance, you can create a view with these clauses and expressions\. Applications can then make relatively simple queries against this view\. If you later find a better way to optimize the original query, when you recreate the view, all the applications immediately take advantage of the optimized base query\. 
+ *Hide the underlying table and column names, and minimize maintenance problems* if those names change\. In that case, you recreate the view using the new names\. All queries that use the view rather than the underlying tables keep running with no changes\.
