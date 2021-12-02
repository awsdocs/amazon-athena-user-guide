# Querying Iceberg Tables and Performing Time Travel<a name="querying-iceberg-tables"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

To query an iceberg dataset, use a standard `SELECT` statement like the following\. Queries follow the Apache Iceberg [format v2 spec](https://iceberg.apache.org/#spec/#format-versioning) and perform merge\-on\-read of both position and equality deletes\.

```
SELECT * FROM databasename.tablename [WHERE predicate]
```

To optimize query times, all predicates are pushed down to where the data lives\.

## Time Travel and Version Travel Queries<a name="querying-iceberg-time-travel-and-version-travel-queries"></a>

Each Apache Iceberg table maintains a versioned manifest of the Amazon S3 objects that it comprises\. Previous versions of the manifest can be used for time travel and version travel queries\.

Time travel queries in Athena query Amazon S3 for historical data from a consistent snapshot as of a specified date and time\. Version travel queries in Athena query Amazon S3 for historical data as of a specified snapshot ID\.

### Time Travel Queries<a name="querying-iceberg-time-travel-queries"></a>

To run a time travel query, use `FOR SYSTEM_TIME AS OF timestamp` after the table name in the `SELECT` statement, as in the following example\.

```
SELECT * FROM database.table FOR SYSTEM_TIME AS OF timestamp
```

The system time to be specified for traveling is either a timestamp or timestamp with a time zone\. If not specified, Athena considers the value to be a timestamp in UTC time\.

The following example time travel queries select CloudTrail data for the specified date and time\.

```
SELECT * FROM iceberg_table FOR SYSTEM_TIME AS OF TIMESTAMP '2020-01-01 10:00:00'
```

```
SELECT * FROM iceberg_table FOR SYSTEM_TIME AS OF (current_timestamp – interval '1' day)
```

### Version Travel Queries<a name="querying-iceberg-version-travel-queries"></a>

To execute a version travel query \(that is, view a consistent snapshot as of a specified version\), use `FOR SYSTEM_VERSION AS OF version` after the table name in the `SELECT` statement, as in the following example\.

```
SELECT * FROM database.table FOR SYSTEM_VERSION AS OF version         
```

The *version* parameter is the `bigint` snapshot ID associated with an Iceberg table version\.

The following example version travel query selects data for the specified version\.

```
SELECT * FROM iceberg_table FOR SYSTEM_VERSION AS OF 949530903748831860
```

#### Retrieving the Snapshot ID<a name="querying-iceberg-table-snapshot-id"></a>

To retrieve the snapshot ID, you can query Iceberg system tables from Athena\.

Use the following syntax to query the Iceberg history system table\.

```
SELECT * FROM "tablename$iceberg_history"
```

This query produces results similar to the following\.

```
made_current_at             | snapshot_id         | parent_id           | is_current_ancestor
----------------------------+---------------------+---------------------+------
2021-02-04 15:40:02.067 UTC | 6022123195077742869 | NULL                | true
2021-02-04 15:46:07.343 UTC | 4479742114237180545 | 6022123195077742869 | true
```

The `snapshot_id` column in the output contains the version numbers that correspond to past states of the table\.

The output has the following schema\.


****  

| **Column** | **Type** | **Description** | 
| --- | --- | --- | 
| made\_current\_at | timestamp with timezone | The time when a snapshot became the current one\. | 
| snapshot\_id | bigint | The ID of the snapshot\. | 
| parent\_id | bigint | The ID of the parent snapshot\. | 
| is\_current\_ancestor | boolean | True if the snapshot is an ancestor of the current snapshot\. | 

### Combining Time and Version Travel<a name="querying-iceberg-combining-time-and-version-travel"></a>

You can use time travel and version travel syntax in the same query to specify different timing and versioning conditions, as in the following example\.

```
SELECT table1.*, table2.* FROM 
  iceberg_table FOR SYSTEM_TIME AS OF (current_timestamp - interval '1' day) AS table1 
  FULL JOIN 
  iceberg_table FOR SYSTEM_VERSION AS OF 5487432386996890161 AS table2 
  ON table1.ts = table2.ts 
  WHERE (table1.id IS NULL OR table2.id IS NULL)
```