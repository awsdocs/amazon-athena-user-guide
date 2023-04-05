# Preventing Amazon S3 throttling<a name="performance-tuning-s3-throttling"></a>

Throttling is the process of limiting the rate at which you use a service, an application, or a system\. In AWS, you can use throttling to prevent overuse of the Amazon S3 service and increase the availability and responsiveness of Amazon S3 for all users\. However, because throttling limits the rate at which the data can be transferred to or from Amazon S3, it's important to consider preventing your interactions from being throttled\.

## Reduce throttling at the service level<a name="performance-tuning-s3-throttling-reduce-throttling-at-the-service-level"></a>

To avoid Amazon S3 throttling at the service level, you can monitor your usage and adjust your [service quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html#limits_s3), or you use certain techniques like partitioning\. The following are some of the conditions that can lead to throttling:
+ **Exceeding your account's API request limits** – Amazon S3 has default API request limits that are based on account type and usage\. If you exceed the maximum number of requests per second for a single object, your requests may be throttled to prevent overload of the Amazon S3 service\.
+ **Insufficient partitioning of data** – If you do not properly partition your data and transfer a large amount of data, Amazon S3 can throttle your requests\. For more information about partitioning, see the [Use partitioning](#performance-tuning-s3-throttling-use-partitioning) section in this document\.
+ **Large number of small objects** – If possible, avoid having a large number of small files\. Amazon S3 has a limit of [5500 requests](https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html) per second, and your Athena queries share this same limit\. If you scan millions of small objects in a single query, your query will likely be throttled by Amazon S3\.

To avoid excess scanning, you can use AWS Glue ETL to periodically compact your files, or you partition the table and add partition key filters\. For more information, see the following resources\.
+ [How can I configure an AWS Glue ETL job to output larger files?](http://aws.amazon.com/premiumsupport/knowledge-center/glue-job-output-large-files/) \(*AWS Knowledge Center*\)
+ [Reading input files in larger groups](https://docs.aws.amazon.com/glue/latest/dg/grouping-input-files.html) \(*AWS Glue Developer Guide*\)

## Optimizing your tables<a name="performance-tuning-s3-throttling-optimizing-your-tables"></a>

Structuring your data is important if you encounter throttling issues\. Although Amazon S3 can handle large amounts of data, throttling sometimes occurs because of the way the data is structured\.

The following sections offer some suggestions on how to structure your data in Amazon S3 to avoid throttling issues\.

### Use partitioning<a name="performance-tuning-s3-throttling-use-partitioning"></a>

You can use partitioning to reduce throttling by limiting the amount of data that has to be accessed at any given time\. By partitioning data on specific columns, you can distribute requests evenly across multiple objects and reduce the number of requests for a single object\. Reducing the amount of data that must be scanned improves query performance and lowers cost\.

You can define partitions, which act as virtual columns, when you create a table\. To create a table with partitions in a `CREATE TABLE` statement, you use the `PARTITIONED BY (column_name data_type)` clause to define the keys to partition your data\.

To restrict the partitions scanned by a query, you can specify them as predicates in a `WHERE` clause of the query\. Thus, columns that are frequently used as filters are good candidates for partitioning\. A common practice is to partition the data based on time intervals, which can lead to a multi\-level partitioning scheme\.

Note that partitioning also has a cost\. When you increase the number of partitions in your table, the time required to retrieve and process partition metadata also increases\. Thus, over\-partitioning can remove the benefits you gain by partitioning more judiciously\. If your data is heavily skewed to one partition value, and most queries use that value, then you may incur the additional overhead\.

For more information about partitioning in Athena, see [What is partitioning?](ctas-partitioning-and-bucketing.md#ctas-partitioning-and-bucketing-what-is-partitioning)

### Bucket your data<a name="performance-tuning-s3-throttling-bucket-your-data"></a>

Another way to partition your data is to bucket the data within a single partition\. With bucketing, you specify one or more columns that contain rows that you want to group together\. Then, you put those rows into multiple buckets\. This way, you query only the bucket that must be read, which reduces the number of rows of data that must be scanned\.

When you select a column to use for bucketing, select the column that has high cardinality \(that is, that has many distinct values\), is uniformly distributed, and is frequently used to filter the data\. An example of a good column to use for bucketing is a primary key, such as an ID column\.

For more information about bucketing in Athena, see [What is bucketing?](ctas-partitioning-and-bucketing.md#ctas-partitioning-and-bucketing-what-is-bucketing)

### Use AWS Glue partition indexes<a name="performance-tuning-s3-throttling-use-aws-glue-partition-indexes"></a>

You can use AWS Glue partition indexes to organize data in a table based on the values of one or more partitions\. AWS Glue partition indexes can reduce the number of data transfers, the amount of data processing, and the time for queries to process\.

An AWS Glue partition index is a metadata file that contains information about the partitions in the table, including the partition keys and their values\. The partition index is stored in an Amazon S3 bucket and is updated automatically by AWS Glue as new partitions are added to the table\.

When an AWS Glue partition index is present, queries attempt to fetch a subset of the partitions instead of loading all the partitions in the table\. Queries only run on the subset of data that is relevant to the query\.

When you create a table in AWS Glue, you can create a partition index on any combination of partition keys defined on the table\. After you have created one or more partition indexes on a table, you must add a property to the table that enables partition filtering\. Then, you can query the table from Athena\.

For information about creating partition indexes in AWS Glue, see [Working with partition indexes in AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/partition-indexes.html) in the *AWS Glue Developer Guide*\. For information about adding a table property to enable partition filtering, see [AWS Glue partition indexing and filtering](glue-best-practices.md#glue-best-practices-partition-index)\.

### Use data compression and file splitting<a name="performance-tuning-s3-throttling-use-data-compression-and-file-splitting"></a>

Data compression can speed up queries significantly if files are at their optimal size or if they can be split into logical groups\. Generally, higher compression ratios require more CPU cycles to compress and decompress the data\. For Athena, we recommend that you use either Apache Parquet or Apache ORC, which compress data by default\. For information about data compression in Athena, see [Athena compression support](compression-formats.md)\.

Splitting files increases parallelism by allowing Athena to distribute the task of reading a single file among multiple readers\. If a single file is not splittable, only a single reader can read the file while other readers are idle\. Apache Parquet and Apache ORC also support splittable files\.

### Use optimized columnar data stores<a name="performance-tuning-s3-throttling-use-optimized-columnar-data-stores"></a>

Athena query performance improves significantly if you convert your data into a columnar format\. When you generate columnar files, one optimization technique to consider is ordering the data based on partition key\.

Apache Parquet and Apache ORC are commonly used open source columnar data stores\. For information on converting existing Amazon S3 data source to one of these formats, see [Converting to columnar formats](convert-to-columnar.md)\.

#### Use a larger Parquet block size or ORC stripe size<a name="performance-tuning-s3-throttling-use-a-larger-parquet-block-size-or-orc-stripe-size"></a>

Parquet and ORC have data storage parameters that you can tune for optimization\. In Parquet, you can optimize for block size\. In ORC, you can optimize for stripe size\. The larger the block or stripe, the more rows that you can store in each\. By default, the Parquet block size is 128 MB, and the ORC stripe size is 64 MB\.

If an ORC stripe is less than 8 MB \(the default value of `hive.orc.max_buffer_size`\), Athena reads the whole ORC stripe\. This is the tradeoff Athena makes between column selectivity and input/output operations per second for smaller stripes\.

If you have tables with a very large number of columns, a small block or stripe size can cause more data to be scanned than necessary\. In these cases, a larger block size can be more efficient\.

#### Use ORC for complex types<a name="performance-tuning-s3-throttling-use-orc-for-complex-types"></a>

Currently, when you query columns stored in Parquet that have complex data types \(for example, `array`, `map`, or `struct`\), Athena reads an entire row of data instead of selectively reading only the specified columns\. This is a known issue in Athena\. As a workaround, consider using ORC\.

#### Choose a compression algorithm<a name="performance-tuning-s3-throttling-choose-a-compression-algorithm"></a>

Another parameter that you can configure is the compression algorithm on data blocks\. For information about the compression algorithms supported for Parquet and ORC in Athena, see [Athena compression support](https://docs.aws.amazon.com/athena/latest/ug/compression-formats.html)\.

For more information about optimization of columnar storage formats in Athena, see the section "Optimize columnar data store generation" in the AWS Big Data Blog post [Top 10 Performance Tuning Tips for Amazon Athena](https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.

### Use Iceberg tables<a name="performance-tuning-s3-throttling-use-iceberg-tables"></a>

Apache Iceberg is an open table format for very large analytic datasets that is designed for optimized usage on Amazon S3\. You can use Iceberg tables to help reduce throttling in Amazon S3\.

Iceberg tables offer you the following advantages:
+ You can partition Iceberg tables on one or more columns\. This optimizes data access and reduces the amount of data that must be scanned by queries\.
+ Because Iceberg object storage mode optimizes Iceberg tables to work with Amazon S3, it can process large volumes of data and heavy query workloads\.
+ Iceberg tables in object storage mode are scalable, fault tolerant, and durable, which can help reduce throttling\.
+ ACID transaction support means that multiple users can add and delete Amazon S3 objects in an atomic manner\.

For more information about Apache Iceberg, see [Apache Iceberg](https://iceberg.apache.org/)\. For more information about using Apache Iceberg tables in Athena, see [Using Iceberg tables](https://docs.aws.amazon.com/athena/latest/ug/querying-iceberg.html)\.

## Optimizing queries<a name="performance-tuning-s3-throttling-optimizing-queries"></a>

Use the suggestions in this section for optimizing your SQL queries in Athena\.

### Use LIMIT with the ORDER BY clause<a name="performance-tuning-s3-throttling-use-limit-with-the-order-by-clause"></a>

The `ORDER BY` clause returns data in a sorted order\. This requires Athena to send all rows of data to a single worker node and then sort the rows\. This type of query can run for a long time or even fail\.

For greater efficiency in your queries, look at the top or bottom *N* values, and then also use a `LIMIT` clause\. This significantly reduces the cost of the sort by pushing both sorting and limiting to individual worker nodes rather than to a single worker\.

### Optimize JOIN clauses<a name="performance-tuning-s3-throttling-optimize-join-clauses"></a>

When you join two tables, Athena distributes the table on the right to worker nodes, and then streams the table on the left to perform the join\.

For this reason, specify the larger table on the left side of the join and the smaller table on the right side of the join\. This way, Athena uses less memory and runs the query with lower latency\.

Also note the following points:
+ When you use multiple `JOIN` commands, specify tables from largest to smallest\.
+ Avoid cross joins unless they are required by the query\.

### Optimize GROUP BY clauses<a name="performance-tuning-s3-throttling-optimize-group-by-clauses"></a>

The `GROUP BY` operator distributes rows based on the `GROUP BY` columns to the worker nodes\. These columns are referenced in memory and the values are compared as the rows are ingested\. The values are aggregated together when the `GROUP BY` column matches\. In consideration of the way this process works, it is advisable to order the columns from the highest cardinality to the lowest\.

### Use numbers instead of strings<a name="performance-tuning-s3-throttling-use-numbers-instead-of-strings"></a>

Because numbers require less memory and are faster to process compared to strings, use numbers instead of strings when possible\.

### Limit the number of columns<a name="performance-tuning-s3-throttling-limit-the-number-of-columns"></a>

To reduce the total amount of memory required to store your data, limit the number of columns specified in your `SELECT` statement\.

### Use regular expressions instead of LIKE<a name="performance-tuning-s3-throttling-use-regular-expressions-instead-of-like"></a>

Queries that include clauses such as `LIKE '%string%'` on large strings can be very computationally intensive\. When you filter for multiple values on a string column, use the [regexp\_like\(\)](https://trino.io/docs/current/functions/regexp.html#regexp_like) function and a regular expression instead\. This is particularly useful when you compare a long list of values\.

### Use the LIMIT clause<a name="performance-tuning-s3-throttling-use-the-limit-clause"></a>

Instead of selecting all columns when you run a query, use the `LIMIT` clause to return only the columns that you require\. This reduces the size of the dataset that is processed through the query execution pipeline\. `LIMIT` clauses are more helpful when you query tables that have a large of number of columns that are string\-based\. `LIMIT` clauses are also helpful when you perform multiple joins or aggregations on any query\.

## See also<a name="performance-tuning-s3-throttling-see-also"></a>

[Best practices design patterns: optimizing Amazon S3 performance](https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html) in the *Amazon Simple Storage Service User Guide*\.

[Performance tuning in Athena](performance-tuning.md)