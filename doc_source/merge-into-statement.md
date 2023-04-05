# MERGE INTO<a name="merge-into-statement"></a>

Conditionally updates, deletes, or inserts rows into an Apache Iceberg table\. A single statement can combine update, delete, and insert actions\.

**Note**  
`MERGE INTO` is transactional and is supported only for Apache Iceberg tables in Athena engine version 3\.

## Synopsis<a name="merge-into-statement-synopsis"></a>

To conditionally update, delete, or insert rows from an Iceberg table, use the following syntax\.

```
MERGE INTO target_table [ [ AS ]  target_alias ]
USING { source_table | query } [ [ AS ] source_alias ]
ON search_condition
when_clause [...]
```

The *when\_clause* is one of the following:

```
WHEN MATCHED [ AND condition ]
    THEN DELETE
```

```
WHEN MATCHED [ AND condition ]
    THEN UPDATE SET ( column = expression [, ...] )
```

```
WHEN NOT MATCHED [ AND condition ]
    THEN INSERT (column_name[, column_name ...]) VALUES (expression, ...)
```

`MERGE` supports an arbitrary number of `WHEN` clauses with different `MATCHED` conditions\. The condition clauses execute the `DELETE`, `UPDATE` or `INSERT` operation in the first `WHEN` clause selected by the `MATCHED` state and the match condition\.

For each source row, the `WHEN` clauses are processed in order\. Only the first matching `WHEN` clause is executed\. Subsequent clauses are ignored\. A user error is raised when a single target table row matches more than one source row\.

If a source row is not matched by any `WHEN` clause and there is no `WHEN NOT MATCHED` clause, the source row is ignored\.

In `WHEN` clauses that have `UPDATE` operations, the column value expressions can refer to any field of the target or the source\. In the `NOT MATCHED` case, the `INSERT` expressions can refer to any field of the source\.

**Example**  
The following example merges rows from the second table into the first table if the rows don't exist in the first table\. Note that the columns listed in the `VALUES` clause must be prefixed by the source table alias\. The target columns listed in the `INSERT` clause must *not* be so prefixed\.

```
MERGE INTO iceberg_table_sample as ice1
USING iceberg2_table_sample as ice2
ON ice1.col1 = ice2.col1
WHEN NOT MATCHED 
THEN INSERT (col1)
      VALUES (ice2.col1)
```

For more `MERGE INTO` examples, see [Updating Iceberg table data](querying-iceberg-updating-iceberg-table-data.md)\.