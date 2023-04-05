# Updating Iceberg table data<a name="querying-iceberg-updating-iceberg-table-data"></a>

Iceberg table data can be managed directly on Athena using `INSERT`, `UPDATE`, and `DELETE` queries\. Each data management transaction produces a new snapshot, which can be queried using time travel\. The `UPDATE` and `DELETE` statements follow the Iceberg format v2 row\-level [position delete](https://iceberg.apache.org/spec/#position-delete-files) specification and enforce snapshot isolation\.

Use the following commands to perform data management operations on Iceberg tables\.

## INSERT INTO<a name="querying-iceberg-insert-into"></a>

Inserts data into an Iceberg table\. Athena Iceberg `INSERT INTO` is charged the same as current `INSERT INTO` queries for external Hive tables by the amount of data scanned\. To insert data into an Iceberg table, use the following syntax, where *query* can be either `VALUES (val1, val2, ...)` or `SELECT (col1, col2, …) FROM [db_name.]table_name WHERE predicate`\. For SQL syntax and semantic details, see [INSERT INTO](insert-into.md)\.

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

Athena Iceberg `DELETE` writes Iceberg position delete files to a table\. This is known as a merge\-on\-read delete\. In contrast to a copy\-on\-write delete, a merge\-on\-read delete is more efficient because it does not rewrite file data\. When Athena reads Iceberg data, it merges the Iceberg position delete files with data files to produce the latest view of a table\. To remove these position delete files, you can run the [REWRITE DATA compaction action](querying-iceberg-data-optimization.md#querying-iceberg-data-optimization-rewrite-data-action)\. `DELETE` operations are charged by the amount of data scanned\. For syntax, see [DELETE](delete-statement.md)\.

The following example deletes rows from `iceberg_table` that have `c3` as the value for `category`\.

```
DELETE FROM iceberg_table WHERE category='c3'
```

## UPDATE<a name="querying-iceberg-update"></a>

Athena Iceberg `UPDATE` writes Iceberg position delete files and newly updated rows as data files in the same transaction\. `UPDATE` can be imagined as a combination of `INSERT INTO` and `DELETE`\. `UPDATE` operations are charged by the amount of data scanned\. For syntax, see [UPDATE](update-statement.md)\.

The following example updates the specified values in the table `iceberg_table`\.

```
UPDATE iceberg_table SET category='c2' WHERE category='c1'
```

## MERGE INTO<a name="querying-iceberg-merge-into"></a>

Conditionally updates, deletes, or inserts rows into an Iceberg table\. A single statement can combine update, delete, and insert actions\. For syntax, see [MERGE INTO](merge-into-statement.md)\.

**Note**  
`MERGE INTO` is transactional and is supported only for Apache Iceberg tables in Athena engine version 3\.

The following example deletes all customers from table `t` that are in the source table `s`\.

```
MERGE INTO accounts t USING monthly_accounts_update s
ON t.customer = s.customer
WHEN MATCHED
THEN DELETE
```

The following example updates target table `t` with customer information from source table `s`\. For customer rows in table `t` that have matching customer rows in table `s`, the example increments the purchases in table t\. If table `t` has no match for a customer row in table `s`, the example inserts the customer row from table `s` into table `t`\.

```
MERGE INTO accounts t USING monthly_accounts_update s
    ON (t.customer = s.customer)
    WHEN MATCHED
        THEN UPDATE SET purchases = s.purchases + t.purchases
    WHEN NOT MATCHED
        THEN INSERT (customer, purchases, address)
              VALUES(s.customer, s.purchases, s.address)
```

The following example conditionally updates target table `t` with information from the source table `s`\. The example deletes any matching target row for which the source address is Centreville\. For all other matching rows, the example adds the source purchases and sets the target address to the source address\. If there is no match in the target table, the example inserts the row from the source table\.

```
MERGE INTO accounts t USING monthly_accounts_update s
    ON (t.customer = s.customer)
    WHEN MATCHED AND s.address = 'Centreville'
        THEN DELETE
    WHEN MATCHED
        THEN UPDATE
            SET purchases = s.purchases + t.purchases, address = s.address
    WHEN NOT MATCHED
        THEN INSERT (customer, purchases, address)
              VALUES(s.customer, s.purchases, s.address)
```