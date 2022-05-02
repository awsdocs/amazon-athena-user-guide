# Updating Iceberg table data<a name="querying-iceberg-updating-iceberg-table-data"></a>

Iceberg table data can be managed directly on Athena using `INSERT`, `UPDATE`, and `DELETE` queries\. Each data management transaction produces a new snapshot, which can be queried using time travel\. The `UPDATE` and `DELETE` statements follow the Iceberg format v2 row\-level [position delete](https://iceberg.apache.org/spec/#position-delete-files) specification and enforce snapshot isolation\.

Use the following commands to perform data management operations on Iceberg tables\.

## INSERT INTO<a name="querying-iceberg-insert-into"></a>

Inserts data into an Iceberg table\. Athena Iceberg `INSERT INTO` is charged the same as current `INSERT INTO` queries for external Hive tables by the amount of data scanned\. To insert data into an Iceberg table, use the following syntax, where *query* can be either `VALUES (val1, val2, ...)` or `SELECT (col1, col2, …) FROM [db_name.]table_name WHERE predicate`\.

```
INSERT INTO [db_name.]table_name [(col1, col2, …)] query
```

The following examples insert values into the table `iceberg_table`\.

```
INSERT INTO iceberg_table VALUES (1,'a','c1')
```

```
INSERT INTO iceberg_table (col1, col2, ...) VALUES (val1, val2, ...)
```

```
INSERT INTO iceberg_table SELECT * FROM another_table
```

## DELETE<a name="querying-iceberg-delete"></a>

Athena Iceberg `DELETE` writes Iceberg position delete files to a table\. This is known as a merge\-on\-read delete\. In contrast to a copy\-on\-write delete, a merge\-on\-read delete is more efficient because it does not rewrite file data\. When Athena reads Iceberg data, it merges the Iceberg position delete files with data files to produce the latest view of a table\. To remove these position delete files, you can run the [REWRITE DATA compaction action](querying-iceberg-data-optimization.md#querying-iceberg-data-optimization-rewrite-data-action)\. `DELETE` operations are charged by the amount of data scanned\. 

To delete rows from a table, use the following syntax\.

```
DELETE FROM [db_name.]table_name [WHERE predicate]
```

The following example deletes rows from `iceberg_table` that have `c3` as the value for `category`\.

```
DELETE FROM iceberg_table WHERE category='c3'
```

## UPDATE<a name="querying-iceberg-update"></a>

Athena Iceberg `UPDATE` writes Iceberg position delete files and newly updated rows as data files in the same transaction\. `UPDATE` can be imagined as a combination of `INSERT INTO` and `DELETE`\. `UPDATE` operations are charged by the amount of data scanned\. 

To update the rows in a table, use the following syntax\.

```
UPDATE [db_name.]table_name SET xx=yy[,...] [WHERE predicate]         
```

The following example updates the specified values in the table `iceberg_table`\.

```
UPDATE iceberg_table SET category='c2' WHERE category='c1'
```