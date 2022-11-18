# SELECT<a name="select"></a>

Retrieves rows of data from zero or more tables\.

**Note**  
This topic provides summary information for reference\. Comprehensive information about using `SELECT` and the SQL language is beyond the scope of this documentation\. For information about using SQL that is specific to Athena, see [Considerations and limitations for SQL queries in Amazon Athena](other-notable-limitations.md) and [Running SQL queries using Amazon Athena](querying-athena-tables.md)\. For an example of creating a database, creating a table, and running a `SELECT` query on the table in Athena, see [Getting started](getting-started.md)\.

## Synopsis<a name="synopsis"></a>

```
[ WITH with_query [, ...] ]
SELECT [ ALL | DISTINCT ] select_expression [, ...]
[ FROM from_item [, ...] ]
[ WHERE condition ]
[ GROUP BY [ ALL | DISTINCT ] grouping_element [, ...] ]
[ HAVING condition ]
[ { UNION | INTERSECT | EXCEPT } [ ALL | DISTINCT ] select ]
[ ORDER BY expression [ ASC | DESC ] [ NULLS FIRST | NULLS LAST] [, ...] ]
[ OFFSET count [ ROW | ROWS ] ]
[ LIMIT [ count | ALL ] ]
```

**Note**  
Reserved words in SQL SELECT statements must be enclosed in double quotes\. For more information, see [List of reserved keywords in SQL SELECT statements](reserved-words.md#list-of-reserved-words-sql-select)\.

## Parameters<a name="select-parameters"></a>

**\[ WITH with\_query \[, \.\.\.\.\] \]**  
You can use `WITH` to flatten nested queries, or to simplify subqueries\.  
 Using the `WITH` clause to create recursive queries is not supported\.  
The `WITH` clause precedes the `SELECT` list in a query and defines one or more subqueries for use within the `SELECT` query\.   
Each subquery defines a temporary table, similar to a view definition, which you can reference in the `FROM` clause\. The tables are used only when the query runs\.   
`with_query` syntax is:  

```
subquery_table_name [ ( column_name [, ...] ) ] AS (subquery)
```
Where:  
+  `subquery_table_name` is a unique name for a temporary table that defines the results of the `WITH` clause subquery\. Each `subquery` must have a table name that can be referenced in the `FROM` clause\.
+  `column_name [, ...]` is an optional list of output column names\. The number of column names must be equal to or less than the number of columns defined by `subquery`\.
+  `subquery` is any query statement\.

**\[ ALL \| DISTINCT \] select\_expr**  
 `select_expr` determines the rows to be selected\.   
 `ALL` is the default\. Using `ALL` is treated the same as if it were omitted; all rows for all columns are selected and duplicates are kept\.  
Use `DISTINCT` to return only distinct values when a column contains duplicate values\.

**FROM from\_item \[, \.\.\.\]**  
Indicates the input to the query, where `from_item` can be a view, a join construct, or a subquery as described below\.  
The `from_item` can be either:  
+  `table_name [ [ AS ] alias [ (column_alias [, ...]) ] ]` 

  Where `table_name` is the name of the target table from which to select rows, `alias` is the name to give the output of the `SELECT` statement, and `column_alias` defines the columns for the `alias` specified\.
 **\-OR\-**   
+  `join_type from_item [ ON join_condition | USING ( join_column [, ...] ) ]` 

  Where `join_type` is one of:
  +  `[ INNER ] JOIN` 
  +  `LEFT [ OUTER ] JOIN` 
  +  `RIGHT [ OUTER ] JOIN` 
  +  `FULL [ OUTER ] JOIN` 
  +  `CROSS JOIN` 
  +  `ON join_condition | USING (join_column [, ...])` Where using `join_condition` allows you to specify column names for join keys in multiple tables, and using `join_column` requires `join_column` to exist in both tables\.

**\[ WHERE condition \]**  
Filters results according to the `condition` you specify, where `condition` generally has the following syntax\.  

```
column_name operator value [[[AND | OR] column_name operator value] ...]
```
The *operator* can be one of the comparators `=`, `>`, `<`, `>=`, `<=`, `<>`, `!=`\.   
The following subquery expressions can also be used in the `WHERE` clause\.  
+ `[NOT] BETWEEN integer_A AND integer_B` – Specifies a range between two integers, as in the following example\. If the column datatype is `varchar`, the column must be cast to integer first\.

  ```
  SELECT DISTINCT processid FROM "webdata"."impressions"
  WHERE cast(processid as int) BETWEEN 1500 and 1800
  ORDER BY processid
  ```
+ `[NOT] LIKE value` – Searches for the pattern specified\. Use the percent sign \(`%`\) as a wildcard character, as in the following example\.

  ```
  SELECT * FROM "webdata"."impressions"
  WHERE referrer LIKE '%.org'
  ```
+ `[NOT] IN (value[, value[, ...])` – Specifies a list of possible values for a column, as in the following example\.

  ```
  SELECT * FROM "webdata"."impressions"
  WHERE referrer IN ('example.com','example.net','example.org')
  ```

**\[ GROUP BY \[ ALL \| DISTINCT \] grouping\_expressions \[, \.\.\.\] \]**  
Divides the output of the `SELECT` statement into rows with matching values\.  
 `ALL` and `DISTINCT` determine whether duplicate grouping sets each produce distinct output rows\. If omitted, `ALL` is assumed\.   
`grouping_expressions` allow you to perform complex grouping operations\. You can use complex grouping operations to perform analysis that requires aggregation on multiple sets of columns in a single query\.  
The `grouping_expressions` element can be any function, such as `SUM`, `AVG`, or `COUNT`, performed on input columns\.   
`GROUP BY` expressions can group output by input column names that don't appear in the output of the `SELECT` statement\.   
All output expressions must be either aggregate functions or columns present in the `GROUP BY` clause\.   
You can use a single query to perform analysis that requires aggregating multiple column sets\.   
Athena supports complex aggregations using `GROUPING SETS`, `CUBE` and `ROLLUP`\. `GROUP BY GROUPING SETS` specifies multiple lists of columns to group on\. `GROUP BY CUBE` generates all possible grouping sets for a given set of columns\. `GROUP BY ROLLUP` generates all possible subtotals for a given set of columns\. Complex grouping operations do not support grouping on expressions composed of input columns\. Only column names are allowed\.   
You can often use `UNION ALL` to achieve the same results as these `GROUP BY` operations, but queries that use `GROUP BY` have the advantage of reading the data one time, whereas `UNION ALL` reads the underlying data three times and may produce inconsistent results when the data source is subject to change\. 

**\[ HAVING condition \]**  
Used with aggregate functions and the `GROUP BY` clause\. Controls which groups are selected, eliminating groups that don't satisfy `condition`\. This filtering occurs after groups and aggregates are computed\.

**\[ \{ UNION \| INTERSECT \| EXCEPT \} \[ ALL \| DISTINCT \] union\_query\] \]**  
`UNION`, `INTERSECT`, and `EXCEPT` combine the results of more than one `SELECT` statement into a single query\. `ALL` or `DISTINCT` control the uniqueness of the rows included in the final result set\.   
`UNION` combines the rows resulting from the first query with the rows resulting from the second query\. To eliminate duplicates, `UNION` builds a hash table, which consumes memory\. For better performance, consider using `UNION ALL` if your query does not require the elimination of duplicates\. Multiple `UNION` clauses are processed left to right unless you use parentheses to explicitly define the order of processing\.  
`INTERSECT` returns only the rows that are present in the results of both the first and the second queries\.  
`EXCEPT` returns the rows from the results of the first query, excluding the rows found by the second query\.  
`ALL` causes all rows to be included, even if the rows are identical\.  
`DISTINCT` causes only unique rows to be included in the combined result set\.

**\[ ORDER BY expression \[ ASC \| DESC \] \[ NULLS FIRST \| NULLS LAST\] \[, \.\.\.\] \]**  
Sorts a result set by one or more output `expression`\.   
When the clause contains multiple expressions, the result set is sorted according to the first `expression`\. Then the second `expression` is applied to rows that have matching values from the first expression, and so on\.   
Each `expression` may specify output columns from `SELECT` or an ordinal number for an output column by position, starting at one\.  
 `ORDER BY` is evaluated as the last step after any `GROUP BY` or `HAVING` clause\. `ASC` and `DESC` determine whether results are sorted in ascending or descending order\.   
The default null ordering is `NULLS LAST`, regardless of ascending or descending sort order\.

**\[ OFFSET count \[ ROW \| ROWS \] \]**  
Use the `OFFSET` clause to discard a number of leading rows from the result set\. If the `ORDER BY` clause is present, the `OFFSET` clause is evaluated over a sorted result set, and the set remains sorted after the skipped rows are discarded\. If the query has no `ORDER BY` clause, it is arbitrary which rows are discarded\. If the count specified by `OFFSET` equals or exceeds the size of the result set, the final result is empty\. 

**LIMIT \[ count \| ALL \]**  
Restricts the number of rows in the result set to `count`\. `LIMIT ALL` is the same as omitting the `LIMIT` clause\. If the query has no `ORDER BY` clause, the results are arbitrary\.

**TABLESAMPLE \[ BERNOULLI \| SYSTEM \] \(percentage\)**  
Optional operator to select rows from a table based on a sampling method\.  
 `BERNOULLI` selects each row to be in the table sample with a probability of `percentage`\. All physical blocks of the table are scanned, and certain rows are skipped based on a comparison between the sample `percentage` and a random value calculated at runtime\.   
With `SYSTEM`, the table is divided into logical segments of data, and the table is sampled at this granularity\.   
Either all rows from a particular segment are selected, or the segment is skipped based on a comparison between the sample `percentage` and a random value calculated at runtime\. `SYSTEM` sampling is dependent on the connector\. This method does not guarantee independent sampling probabilities\.

**\[ UNNEST \(array\_or\_map\) \[WITH ORDINALITY\] \]**  
Expands an array or map into a relation\. Arrays are expanded into a single column\. Maps are expanded into two columns \(*key*, *value*\)\.   
You can use `UNNEST` with multiple arguments, which are expanded into multiple columns with as many rows as the highest cardinality argument\.   
Other columns are padded with nulls\.   
The `WITH ORDINALITY` clause adds an ordinality column to the end\.  
 `UNNEST` is usually used with a `JOIN` and can reference columns from relations on the left side of the `JOIN`\.

## Getting the file locations for source data in Amazon S3<a name="select-path"></a>

To see the Amazon S3 file location for the data in a table row, you can use `"$path"` in a `SELECT` query, as in the following example:

```
SELECT "$path" FROM "my_database"."my_table" WHERE year=2019;
```

This returns a result like the following:

```
s3://awsexamplebucket/datasets_mytable/year=2019/data_file1.json
```

To return a sorted, unique list of the S3 filename paths for the data in a table, you can use `SELECT DISTINCT` and `ORDER BY`, as in the following example\.

```
SELECT DISTINCT "$path" AS data_source_file
FROM sampledb.elb_logs
ORDER By data_source_file ASC
```

To return only the filenames without the path, you can pass `"$path"` as a parameter to an `regexp_extract` function, as in the following example\.

```
SELECT DISTINCT regexp_extract("$path", '[^/]+$') AS data_source_file
FROM sampledb.elb_logs
ORDER By data_source_file ASC
```

To return the data from a specific file, specify the file in the `WHERE` clause, as in the following example\.

```
SELECT *,"$path" FROM my_database.my_table WHERE "$path" = 's3://awsexamplebucket/my_table/my_partition/file-01.csv'
```

For more information and examples, see the Knowledge Center article [How can I see the Amazon S3 source file for a row in an Athena table?](http://aws.amazon.com/premiumsupport/knowledge-center/find-s3-source-file-athena-table-row/)\.

## Escaping single quotes<a name="select-escaping"></a>

 To escape a single quote, precede it with another single quote, as in the following example\. Do not confuse this with a double quote\. 

```
Select 'O''Reilly'
```

**Results**  
`O'Reilly`

## Additional resources<a name="select-additional-resources"></a>

For more information about using `SELECT` statements in Athena, see the following resources\.


| For information about this | See this | 
| --- | --- | 
| Running queries in Athena | [Running SQL queries using Amazon Athena](querying-athena-tables.md) | 
| Using SELECT to create a table | [Creating a table from query results \(CTAS\)](ctas.md) | 
| Inserting data from a SELECT query into another table | [INSERT INTO](insert-into.md) | 
| Using built\-in functions in SELECT statements | [Functions in Amazon Athena](functions.md) | 
| Using user defined functions in SELECT statements | [Querying with user defined functions](querying-udf.md) | 
| Querying Data Catalog metadata | [Querying AWS Glue Data Catalog](querying-glue-catalog.md) | 