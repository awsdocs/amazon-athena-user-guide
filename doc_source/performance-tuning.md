# Performance Tuning in Athena<a name="performance-tuning"></a>

This topic provides general information and specific suggestions for improving the performance of Athena when you have large amounts of data and experience memory usage or performance issues\.

## Physical Limits<a name="performance-tuning-physical-limits"></a>

By default, Athena [limits](service-limits.md) the runtime of DML queries to 30 minutes and DDL queries to 600 minutes\. Queries that run beyond these limits are automatically cancelled without charge\. If a query runs out of memory or a node crashes during processing, errors like the following can occur:

```
INTERNAL_ERROR_QUERY_ENGINE
```

```
EXCEEDED_MEMORY_LIMIT: Query exceeded local memory limit
```

```
Query exhausted resources at this scale factor
```

```
Encountered too many errors talking to a worker node. The node may have crashed or be under too much load.
```

## Query Optimization Techniques<a name="performance-tuning-query-optimization-techniques"></a>

For queries that require resources beyond existing limits, you can either optimize the query or restructure the data being queried\. To optimize your queries, consider the suggestions in this section\.
+ [Data Size](#performance-tuning-data-size)
+ [File Formats](#performance-tuning-file-formats)
+ [Joins, Grouping, and Unions](#performance-tuning-joins-grouping-and-unions)
+ [Partitioning](#performance-tuning-partitioning)
+ [Window Functions](#performance-tuning-window-functions)
+ [Use More Efficient Functions](#performance-tuning-use-more-efficient-functions)

### Data Size<a name="performance-tuning-data-size"></a>

**Avoid single large files** – If your file size is extremely large, try to break up the file into smaller files and use partitions to organize them\.

**Read a smaller amount of data at once** – Scanning a large amount of data at one time can slow down the query and increase cost\. Use partitions or filters to limit the files to be scanned\.

**Avoid having too many columns** – The message GENERIC\_INTERNAL\_ERROR: io\.airlift\.bytecode\.CompilationException can occur when Athena fails to compile the query to bytecode\. This exception is usually caused by having too many columns in the query\. Reduce the number of the columns in the query or create subqueries and use a `JOIN` that retrieves a smaller amount of data\.

**Avoid large query outputs** – A large amount of output data can slow performance\. To work around this, try using [CTAS](create-table-as.md) to create a new table with the result of the query or [INSERT INTO](insert-into.md) to append new results into an existing table\.

**Avoid CTAS queries with a large output** – CTAS queries can also use a large amount of memory\. If you are outputting large amount of data, try separating the task into smaller queries\.

** If possible, avoid having a large number of small files** – Amazon S3 has a [limit](https://docs.aws.amazon.com/AmazonS3/latest/dev/optimizing-performance.html) of 5500 requests per second\. Athena queries share the same limit\. If you need to scan millions of small objects in a single query, your query can be easily throttled by Amazon S3\. To avoid excessive scanning, use AWS Glue ETL to periodically compact your files or partition the table and add partition key filters\. For more information, see [Reading Input Files in Larger Groups](https://docs.aws.amazon.com/glue/latest/dg/grouping-input-files.html) in the AWS Glue Developer Guide or [How can I configure an AWS Glue ETL job to output larger files?](http://aws.amazon.com/premiumsupport/knowledge-center/glue-job-output-large-files/) in the AWS Knowledge Center\.

**Avoid scanning an entire table** – Use the following techniques to avoid scanning entire tables:
+ Limit the use of "`*`"\. Try not to select all columns unless necessary\.
+ Avoid scanning the same table multiple times in the same query
+ Use filters to reduce the amount of data to be scanned\.
+ Whenever possible, add a `LIMIT` clause\.

** Avoid referring to many views and tables in a single query** – Because queries with many views and/or tables must load a large amount of data, out of memory errors can occur\. If possible, avoid referring to an excessive number of views or tables in a single query\.

**Avoid large JSON strings** – If data is stored in a single JSON string and the size of the JSON data is large, out of memory errors can occur when the JSON data is processed\.

### File Formats<a name="performance-tuning-file-formats"></a>

**Use an efficient file format such as Parquet or ORC** – To dramatically reduce query running time and costs, use compressed Parquet or ORC files to store your data\. To convert your existing dataset to those formats in Athena, you can use CTAS\. For more information, see [Using CTAS and INSERT INTO for ETL and Data Analysis](ctas-insert-into-etl.md)\.

**Switch between ORC and Parquet formats** – Experience shows that the same set of data can have significant differences in processing time depending on whether it is stored in ORC or Parquet format\. If you are experiencing performance issues, try a different format\.

**Hudi queries** – Because [Hudi queries](querying-hudi.md) bypass the native reader and split generator for files in parquet format, they can be slow\. Keep this in mind when querying Hudi datasets\.

### Joins, Grouping, and Unions<a name="performance-tuning-joins-grouping-and-unions"></a>

**Reduce the usage of memory intensive operations** – Operations like `JOIN`, `GROUP BY`, `ORDER BY`, and `UNION` all require loading large amount of data into memory\. To speed up your query, find other ways to achieve the same results, or add a clause like `LIMIT` to the outer query whenever possible\.

**Consider using UNION ALL** – To eliminate duplicates, `UNION` builds a hash table, which consumes memory\. If your query does not require the elimination of duplicates, consider using `UNION ALL` for better performance\.

**Use CTAS as an intermediary step to speed up JOIN operations** – Instead of loading and processing intermediary data with every query, use CTAS to persist the intermediary data into Amazon S3\. This can help speed up the performance of operations like `JOIN`\.

### Partitioning<a name="performance-tuning-partitioning"></a>

**Limit the number of partitions in a table** – When a table has more than 100,000 partitions, queries can be slow because of the large number of requests sent to AWS Glue to retrieve partition information\. To resolve this issue, try one of the following options:
+ Use [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md) to remove stale partitions\.
+ If your partition pattern is predictable, use [partition projection](partition-projection.md)\.

**Remove old partitions even if they are empty** – Even if a partition is empty, the metadata of the partition is still stored in AWS Glue\. Loading these unneeded partitions can increase query runtimes\. To remove the unneeded partitions, use [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md)\.

**Look up a single partition** – When looking up a single partition, try to provide all partition values so that Athena can locate the partition with a single call to AWS Glue\. Otherwise, Athena must retrieve all partitions and filter them\. This can be costly and greatly increase the planning time for your query\. If you have a predictable partition pattern, you can use [partition projection](partition-projection.md) to avoid the partition look up calls to AWS Glue\.

**Set reasonable partition projection properties** – When using [partition projection](partition-projection.md), Athena tries to create a partition object for every partition name\. Because of this, make sure that the table properties that you define do not create a near infinite amount of possible partitions\.

**To add new partitions frequently, use `ALTER TABLE ADD PARTITION`** – If you use `MSCK REPAIR TABLE` to add new partitions frequently \(for example, on a daily basis\) and are experiencing query timeouts, consider using [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)\. `MSCK REPAIR TABLE` is best used when creating a table for the first time or when there is uncertainty about parity between data and partition metadata\.

**Avoid using coalesce\(\) in a WHERE clause with partitioned columns** – Under some circumstances, using the [coalesce\(\)](https://prestodb.io/docs/0.217/functions/conditional.html#coalesce) or other functions in a `WHERE` clause against partitioned columns might result in reduced performance\. If this occurs, try rewriting your query to provide the same functionality without using `coalesce()`\.

### Window Functions<a name="performance-tuning-window-functions"></a>

**Minimize the use of window functions** – [Window functions](https://prestodb.io/docs/0.217/functions/window.html) such as [rank\(\)](https://prestodb.io/docs/0.217/functions/window.html#rank) are memory intensive\. In general, window functions require an entire dataset to be loaded into a single Athena node for processing\. With an extremely large dataset, this can risk crashing the node\. To avoid this, try the following options:
+ Filter the data and run window functions on a subset of the data\.
+ Use the `PARTITION BY` clause with the window function whenever possible\.
+ Find an alternative way to construct the query\.

### Use More Efficient Functions<a name="performance-tuning-use-more-efficient-functions"></a>

**Replace `row_number() OVER (...) as rnk ... WHERE rnk = 1`** – To speed up a query with a [row\_number\(\)](https://prestodb.io/docs/0.217/functions/window.html#row_number) clause like this, replace this syntax with a combination of `GROUP BY`, `ORDER BY`, and `LIMIT 1`\.

**Use regular expressions instead of `LIKE` on large strings** – Queries that include clauses such as `LIKE '%string%'` on large strings can be very costly\. Consider using the [regexp\_like\(\)](https://prestodb.io/docs/0.217/functions/regexp.html#regexp_like) function and a regular expression instead\.

**Use max\(\) instead of element\_at\(array\_sort\(\), 1\)** – For increased speed, replace the nested functions `element_at(array_sort(), 1)` with [max\(\)](https://prestodb.io/docs/0.217/functions/aggregate.html#max)\.

## Additional Resources<a name="performance-tuning-additional-resources"></a>

For additional information on performance tuning in Athena, consider the following resources:
+ Read the AWS Big Data blog post [Top 10 Performance Tuning Tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)
+ Read other [Athena posts in the AWS Big Data Blog](http://aws.amazon.com/blogs/big-data/tag/amazon-athena/) 
+ Ask a question on [AWS re:Post](https://repost.aws/tags/TA78iVOM7gR62_QqDe2-CmiA/amazon-athena) using the **Amazon Athena** tag
+ Consult the [Athena topics in the AWS Knowledge Center](https://aws.amazon.com/premiumsupport/knowledge-center/#Amazon_Athena) 
+ Contact AWS Support \(in the AWS Management Console, click **Support**, **Support Center**\)