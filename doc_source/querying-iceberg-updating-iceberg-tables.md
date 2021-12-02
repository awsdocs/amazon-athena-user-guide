# Updating Iceberg Tables<a name="querying-iceberg-updating-iceberg-tables"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

Iceberg table data can be managed directly on Athena using `INSERT`, `UPDATE`, and `DELETE` queries\. Each data management transaction produces a new snapshot, which can be queried using time travel\. The `UPDATE` and `DELETE` statements follow the Iceberg format v2 row\-level [position delete](https://iceberg.apache.org/#spec/#position-delete-files) specification\.

Use the following commands to perform data management operations on Iceberg tables\.

## INSERT INTO<a name="querying-iceberg-insert-into"></a>

To insert data into an Iceberg table, use the following syntax, where *query* can be either `VALUES (val1, val2, ...)` or `SELECT (col1, col2, …) FROM table WHERE predicate`\.

```
INSERT INTO databasename.tablename [(col1, col2, …)] query
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

## UPDATE<a name="querying-iceberg-update"></a>

To update the rows in a table, use the following syntax\.

```
UPDATE databasename.tablename SET xx=yy[,...] [WHERE predicate]         
```

The following example updates the specified values in the table **iceberg\_table**\.

```
UPDATE iceberg_table SET category='c2' WHERE category='c1'
```

## DELETE<a name="querying-iceberg-delete"></a>

To delete rows from a table, use the following syntax\.

```
DELETE FROM databasename.tablename [WHERE predicate]
```

The following example deletes rows from `iceberg_table` that have `c3` as the value for `category`\.

```
DELETE FROM iceberg_table WHERE category='c3'
```