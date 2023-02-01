# Bucketing vs partitioning<a name="ctas-bucketing-vs-partitioning"></a>

You can specify partitioning and bucketing, for storing data from CTAS query results in Amazon S3\. For information about CTAS queries, see [CREATE TABLE AS SELECT \(CTAS\)](ctas.md)\.

This section discusses partitioning and bucketing as they apply to CTAS queries only\. For general guidelines about using partitioning in CREATE TABLE queries, see [Top performance tuning tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.

Use the following tips to decide whether to partition and/or to configure bucketing, and to select columns in your CTAS queries by which to do so:
+ *Partitioning CTAS query results* works well when the number of partitions you plan to have is limited\. When you run a CTAS query, Athena writes the results to a specified location in Amazon S3\. If you specify partitions, it creates them and stores each partition in a separate partition folder in the same location\. The maximum number of partitions you can configure with CTAS query results in one query is 100\. However, you can work around this limitation\. For more information, see [Using CTAS and INSERT INTO to create a table with more than 100 partitions](ctas-insert-into.md)\.

  Having partitions in Amazon S3 helps with Athena query performance, because this helps you run targeted queries for only specific partitions\. Athena then scans only those partitions, saving you query costs and query time\. For information about partitioning syntax, search for `partitioned_by` in [CREATE TABLE AS](create-table-as.md)\.

  Partition data by those columns that have similar characteristics, such as records from the same department, and that can have a limited number of possible values, such as a limited number of distinct departments in an organization\. This characteristic is known as *data cardinality*\. For example, if you partition by the column `department`, and this column has a limited number of distinct values, partitioning by `department` works well and decreases query latency\. 
+ *Bucketing CTAS query results* works well when you bucket data by the column that has high cardinality and evenly distributed values\. 

  For example, columns storing `timestamp` data could potentially have a very large number of distinct values, and their data is evenly distributed across the data set\. This means that a column storing `timestamp` type data will most likely have values and won't have nulls\. This also means that data from such a column can be put in many buckets, where each bucket will have roughly the same amount of data stored in Amazon S3\. 

  To choose the column by which to bucket the CTAS query results, use the column that has a high number of values \(high cardinality\) and whose data can be split for storage into many buckets that will have roughly the same amount of data\. Columns that are sparsely populated with values are not good candidates for bucketing\. This is because you will end up with buckets that have less data and other buckets that have a lot of data\. By comparison, columns that you predict will almost always have values, such as `timestamp` type values, are good candidates for bucketing\. This is because their data has high cardinality and can be stored in roughly equal chunks\. 

  For more information about bucketing syntax, search for `bucketed_by` in [CREATE TABLE AS](create-table-as.md)\. 

To conclude, you can partition and use bucketing for storing results of the same CTAS query\. These techniques for writing data do not exclude each other\. Typically, the columns you use for bucketing differ from those you use for partitioning\. 

For example, if your dataset has columns `department`, `sales_quarter`, and `customer_id` \(integer type\), you can partition your CTAS query results by `department` and `sales_quarter`\. These columns have relatively low cardinality of values: a limited number of departments and sales quarters\. Also, for partitions, it does not matter if some records in your dataset have null or no values assigned for these columns\. What matters is that data with the same characteristics, such as data from the same department, will be in one partition that you can query in Athena\. 

At the same time, because all of your data has integer type values stored in the column `customer_id`, you can configure bucketing for the same query results by the column `customer_id`\. This column has high cardinality\. You can store its data in more than one bucket in Amazon S3\. Consider an opposite scenario: if you don't create buckets for the column `customer_id` and run a query for particular customer ID values, then you would have to scan a very large amount of data stored in a single location in Amazon S3\. Instead, if you configure buckets for storing your ID\-related results, you can only scan and query buckets that have your value and avoid long\-running queries that scan a large amount of data\. 

## Data types supported for filtering on bucketed columns<a name="bucketing-data-types-supported-for-filtering"></a>

You can reduce the amount of data scanned by adding filters on bucketed columns that have certain data types\. Athena supports such filtering only on bucketed columns with the following data types:
+  `TINYINT` 
+  `SMALLINT` 
+  `INT` 
+  `BIGINT` 
+  `BOOLEAN` 
+  `STRING` 
+  `TIMESTAMP` 