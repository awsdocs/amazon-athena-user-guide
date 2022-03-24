# Dynamic ID Partitioning<a name="partition-projection-dynamic-id-partitioning"></a>

You might have tables partitioned on a unique identifier column that has the following characteristics:
+ Adds new values frequently, perhaps automatically\.
+ Cannot be easily generated\. They might be user names or device IDs of varying composition or length, not sequential integers within a defined range\.

For such partitioning schemes, the `enum` projection type would be impractical for the following reasons:
+ You would have to modify the table properties each time a value is added to the table\.
+ A single table property would have millions of characters or more\.
+ Projection requires that all partition columns be configured for projection\. This requirement could not be avoided for only one column\.

To overcome these limitations, you can use `injection` or `bucketing`\.

## Injection<a name="partition-projection-injection"></a>

If your query pattern on a dynamic ID dataset always specifies a single value for the high cardinality partition column, you can use value injection\. Injection avoids the need to project the full partition space\.

Imagine that you want to partition an IoT dataset on a UUID field that has extremely high cardinality like `device_id`\. The field has the following characteristics:
+ An extremely high number \(potentially billions\) of values\.
+ Because its values are random strings, it is not projectable using other projection methods\.
+ The extremely large number of partitions cannot be stored in commonly used metastores\.

However, if all of your queries include a `WHERE` clause that filters for only a single `device_id`, you can use the following approach in your `CREATE TABLE` statement\.

```
...
 PARTITIONED BY
 (
     device_id STRING
 )
 LOCATION "s3://bucket/prefix/"
 TBLPROPERTIES
 (
     "projection.enabled" = "true",
     "projection.device_id.type" = "injected",
     "storage.location.template" = "s3://bucket/prefix/${device_id}"
 )
```

A `SELECT` query on a table like this looks like the following:

```
SELECT
  col1,
  col2,...,
  device_id
FROM
  table
WHERE
  device_id = "b6319dc2-48c1-4cd5-a0a3-a1969f7b48f7"
  AND (
    col1 > 0
    or col2 < 10
  )
```

In the example, Athena projects only a single partition for any given query\. This avoids the need to store and act upon millions or billions of virtual partitions only to find one partition and read from it\.

The following example shows a query that uses multiple injected values for a column:

```
SELECT * FROM logs
WHERE service IN ('kafka' OR 'SQS' OR 'email' OR ...)
AND datehour >= '2020/01/01/00'
```

## Bucketing<a name="partition-projection-bucketing"></a>

In the bucketing technique, you use a fixed set of bucket values rather than the entire set of identifiers for your partitioning\. If you can map an identifier to a bucket, you can use this mapping in your queries\. You still benefit as when you partition on the identifiers themselves\.

Bucketing has the following advantages over injection:
+ You can specify more than one value at a time for a field in the `WHERE` clause\.
+ You can continue to use your partitions with more traditional metastores\.

Using the scenario in the previous example and assuming 1 million buckets, identified by an integer, the `CREATE TABLE` statement becomes the following\.

```
...
 PARTITIONED BY
 (
     BUCKET_ID BIGINT
 )
 LOCATION "s3://bucket/prefix/"
 TBLPROPERTIES
 (
     "projection.enabled" = "true",
     "projection.bucket_id.type" = "integer",
     "projection.bucket_id.range" = "1,1000000"
 )
```

A corresponding `SELECT` query uses a mapping function in the `WHERE` clause, as in the following example\.

```
SELECT
  col1,
  col2,...,
  identifier
FROM
  table
WHERE
  bucket_id = map_identifier_to_bucket("ID-IN-QUESTION")
  AND identifier = "ID-IN-QUESTION"
```

Replace the *map\_identifier\_to\_bucket* function in the example with any scalar expression that maps an identifier to an integer\. For example, the expression could be a simple hash or modulus\. The function enforces a constant upper bound on the number of partitions that can ever be projected on the specified dimension\. When paired with a file format that supports predicate pushdown such as Apache Parquet or ORC, the bucket technique provides good performance\.

For information on writing your own user\-defined function like the scalar bucketing function in the preceding example, see [Querying with User Defined Functions](querying-udf.md)\.