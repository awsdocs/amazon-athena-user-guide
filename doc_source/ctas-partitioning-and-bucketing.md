# Partitioning and bucketing in Athena<a name="ctas-partitioning-and-bucketing"></a>

Partitioning and bucketing are two ways to reduce the amount of data Athena must scan when you run a query\. Partitioning and bucketing are complementary and can be used together\. Reducing the amount of data scanned leads to improved performance and lower cost\. For general guidelines about Athena query performance, see [Top 10 performance tuning tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.

## What is partitioning?<a name="ctas-partitioning-and-bucketing-what-is-partitioning"></a>

Partitioning means organizing data into directories \(or "prefixes"\) on Amazon S3 based on a particular property of the data\. Such properties are called partition keys\. A common partition key is the date or some other unit of time such as the year or month\. However, a dataset can be partitioned by more than one key\. For example, data about product sales might be partitioned by date, product category, and market\.

### Deciding how to partition<a name="ctas-partitioning-and-bucketing-deciding-how-to-partition"></a>

Good candidates for partition keys are properties that are always or frequently used in queries and have low cardinality\. There is a trade\-off between having too many partitions and having too few\. With too many partitions, the increased number of files creates overhead\. There is also some overhead from filtering the partitions themselves\. With too few partitions, queries often have to scan more data\.

### Creating a partitioned table<a name="ctas-partitioning-and-bucketing-creating-a-partitioned-table"></a>

When a dataset is partitioned, you can create a partitioned table in Athena\. A partitioned table is a table that has partition keys\. When you use `CREATE TABLE`, you add partitions to the table\. When you use `CREATE TABLE AS`, the partitions that are created on Amazon S3 are automatically added to the table\.

In a `CREATE TABLE` statement, you specify the partition keys in the `PARTITIONED BY (column_name data_type)` clause\. In a `CREATE TABLE AS` statement, you specify the partition keys in a `WITH (partitioned_by = ARRAY['partition_key'])` clause, or `WITH (partitioning = ARRAY['partition_key'])` for Iceberg tables\. For performance reasons, partition keys should always be of type `STRING`\. For more information, see [Use string as the data type for partition keys](data-types.md#data-types-timestamps-partition-key-types)\.

For additional `CREATE TABLE` and `CREATE TABLE AS` syntax details, see [CREATE TABLE](create-table.md) and [CTAS table properties](create-table-as.md#ctas-table-properties)\.

### Querying partitioned tables<a name="ctas-partitioning-and-bucketing-querying-partitioned-tables"></a>

When you query a partitioned table, Athena uses the predicates in the query to filter the list of partitions\. Then it uses the locations of the matching partitions to process the files found\. Athena can efficiently reduce the amount of data scanned by simply not reading data in the partitions that don't match the query predicates\.

#### Examples<a name="ctas-partitioning-and-bucketing-partitioned-table-example-queries"></a>

Suppose you have a table partitioned by `sales_date` and `product_category` and want to know the total revenue over a week in a specific category\. You include predicates on the `sales_date` and `product_category` columns to ensure that Athena scans only the minimum amount of data, as in the following example\.

```
SELECT SUM(amount) AS total_revenue 
FROM sales 
WHERE sales_date BETWEEN '2023-02-27' AND '2023-03-05' 
AND product_category = 'Toys'
```

Suppose you have a dataset that is partitioned by date but also has a fine\-grained timestamp\.

With Iceberg tables, you can declare a partition key to have a relationship to a column, but with Hive tables the query engine has no knowledge of relationships between columns and partition keys\. For this reason, you must include a predicate on both the column and the partition key in your query to make sure the query does not scan more data than necessary\.

For example, suppose the `sales` table in the previous example also has a `sold_at` column of the `TIMESTAMP` data type\. If you want the revenue only for a specific time range, you would write the query like this:

```
SELECT SUM(amount) AS total_revenue 
FROM sales 
WHERE sales_date = '2023-02-28' 
AND sold_at BETWEEN TIMESTAMP '2023-02-28 10:00:00' AND TIMESTAMP '2023-02-28 12:00:00' 
AND product_category = 'Toys'
```

For more information about this difference between querying Hive and Iceberg tables, see [How to write queries for timestamp fields that are also time\-partitioned](data-types.md#data-types-timestamps-how-to-write-queries-for-timestamp-fields-that-are-also-time-partitioned)\.

## What is bucketing?<a name="ctas-partitioning-and-bucketing-what-is-bucketing"></a>

Bucketing is a way to organize the records of a dataset into categories called buckets\.

This meaning of bucket and bucketing is different from, and should not be confused with, Amazon S3 buckets\. In data bucketing, records that have the same value for a property go into the same bucket\. Records are distributed as evenly as possible among buckets so that each bucket has roughly the same amount of data\.

In practice, the buckets are files, and a hash function determines the bucket that a record goes into\. A bucketed dataset will have one or more files per bucket per partition\. The bucket that a file belongs to is encoded in the file name\.

### Bucketing benefits<a name="ctas-partitioning-and-bucketing-bucketing-benefits"></a>

Bucketing is useful when a dataset is bucketed by a certain property and you want to retrieve records in which that property has a certain value\. Because the data is bucketed, Athena can use the value to determine which files to look at\. For example, suppose a dataset is bucketed by `customer_id` and you want to find all records for a specific customer\. Athena determines the bucket that contains those records and only reads the files in that bucket\.

Good candidates for bucketing occur when you have columns that have high cardinality \(that is, have many distinct values\), are uniformly distributed, and that you frequently query for specific values\.

**Note**  
Athena does not support using `INSERT INTO` to add new records to bucketed tables\.

### Data types supported for filtering on bucketed columns<a name="ctas-partitioning-and-bucketing-data-types-supported-for-filtering-on-bucketed-columns"></a>

You can add filters on bucketed columns with certain data types\. Athena supports filtering on bucketed columns with the following data types:
+ BOOLEAN
+ BYTE
+ DATE
+ DOUBLE
+ FLOAT
+ INT
+ LONG
+ SHORT
+ STRING
+ VARCHAR

### Hive and Spark support<a name="ctas-partitioning-and-bucketing-hive-and-spark-support"></a>

Athena engine version 2 supports datasets bucketed using the Hive bucket algorithm, and Athena engine version 3 also supports the Apache Spark bucketing algorithm\. Hive bucketing is the default\. If your dataset is bucketed using the Spark algorithm, use the `TBLPROPERTIES` clause to set the `bucketing_format` property value to `spark`\.

### Bucketing CREATE TABLE example<a name="ctas-partitioning-and-bucketing-bucketing-create-table-example"></a>

To create a table for an existing bucketed dataset, use the `CLUSTERED BY (column)` clause followed by the `INTO N BUCKETS` clause\. The `INTO N BUCKETS` clause specifies the number of buckets the data is bucketed into\.

In the following `CREATE TABLE` example, the `sales` dataset is bucketed by `customer_id` into 8 buckets using the Spark algorithm\. The `CREATE TABLE` statement uses the `CLUSTERED BY` and `TBLPROPERTIES` clauses to set the properties accordingly\.

```
CREATE EXTERNAL TABLE sales (...) 
... 
CLUSTERED BY (`customer_id`) INTO 8 BUCKETS 
... 
TBLPROPERTIES ( 
  'bucketing_format' = 'spark' 
)
```

### Bucketing CREATE TABLE AS \(CTAS\) example<a name="ctas-partitioning-and-bucketing-bucketing-create-table-as-example"></a>

To specify bucketing with `CREATE TABLE AS`, use the `bucketed_by` and `bucket_count` parameters, as in the following example\.

```
CREATE TABLE sales 
WITH ( 
  ... 
  bucketed_by = ARRAY['customer_id'], 
  bucket_count = 8 
) 
AS SELECT ...
```

### Bucketing query example<a name="ctas-partitioning-and-bucketing-bucketing-query-example"></a>

The following example query looks for the names of products that a specific customer has purchased over the course of a week\.

```
SELECT DISTINCT product_name 
FROM sales 
WHERE sales_date BETWEEN '2023-02-27' AND '2023-03-05' 
AND customer_id = 'c123'
```

If this table is partitioned by `sales_date` and bucketed by `customer_id`, Athena can calculate the bucket that the customer records are in\. At most, Athena reads one file per partition\.

## See also<a name="ctas-partitioning-and-bucketing-see-also"></a>

For a `CREATE TABLE AS` example that creates both bucketed and partitioned tables, see [Example: Creating bucketed and partitioned tables](https://docs.aws.amazon.com/athena/latest/ug/ctas-examples.html#ctas-example-bucketed)\.