# Optimizing Iceberg tables<a name="querying-iceberg-data-optimization"></a>

As data accumulates into an Iceberg table, queries gradually become less efficient because of the increased processing time required to open files\. Additional computational cost is incurred if the table contains [delete files](https://iceberg.apache.org/spec/#position-delete-files)\. In Iceberg, delete files store row\-level deletes, and the engine must apply the deleted rows to query results\.

To help optimize the performance of queries on Iceberg tables, Athena supports manual compaction as a table maintenance command\. Compactions optimize the structural layout of the table without altering table content\.

## OPTIMIZE<a name="querying-iceberg-data-optimization-rewrite-data-action"></a>

The `OPTIMIZE table REWRITE DATA` compaction action rewrites data files into a more optimized layout based on their size and number of associated delete files\. For syntax and table property details, see [OPTIMIZE](optimize-statement.md)\.

### Example<a name="querying-iceberg-data-optimization-example"></a>

The following example merges delete files into data files and produces files near the targeted file size where the value of `catalog` is `c1`\.

```
OPTIMIZE iceberg_table REWRITE DATA USING BIN_PACK
  WHERE catalog = 'c1'
```

## VACUUM<a name="querying-iceberg-vacuum"></a>

`VACUUM` performs [snapshot expiration](https://iceberg.apache.org/docs/latest/spark-procedures/#expire_snapshots) and [orphan file removal](https://iceberg.apache.org/docs/latest/spark-procedures/#remove_orphan_files)\. These actions reduce metadata size and remove files not in the current table state that are also older than the retention period specified for the table\. For syntax details, see [VACUUM](vacuum-statement.md)\.

## Example<a name="querying-iceberg-vacuum-example"></a>

The following example uses a table property to configure the table `iceberg_table` to retain the last three days of data, then uses `VACUUM` to expire the old snapshots and remove the orphan files from the table\.

```
ALTER TABLE iceberg_table SET TBLPROPERTIES (
  'vacuum_max_snapshot_age_seconds'='259200'
)

VACUUM iceberg_table
```