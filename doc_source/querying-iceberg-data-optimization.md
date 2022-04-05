# Optimizing Iceberg Tables<a name="querying-iceberg-data-optimization"></a>

As data accumulates into an Iceberg table, queries gradually become less efficient because of the increased processing time required to open files\. Additional computational cost is incurred if the table contains [delete files](https://iceberg.apache.org/spec/#position-delete-files)\. In Iceberg, delete files store row\-level deletes, and the engine must apply the deleted rows to query results\.

To help optimize the performance of queries on Iceberg tables, Athena supports manual compaction as a table maintenance command\. Compactions optimize the structural layout of the table without altering table content\.

## REWRITE DATA Compaction Action<a name="querying-iceberg-data-optimization-rewrite-data-action"></a>

The `REWRITE DATA` compaction action rewrites data files into a more optimized layout based on their size and number of associated delete files\.

### Syntax<a name="querying-iceberg-data-optimization-rewrite-data-action-syntax"></a>

The following syntax summary shows how to optimize data layout for an Iceberg table\.

```
OPTIMIZE [db_name.]table_name REWRITE DATA
  USING BIN_PACK
  [WHERE predicate]
```

The compaction action is charged by the amount of data scanned during the rewrite process\. The `REWRITE DATA` action uses predicates to select for files that contain matching rows\. If any row in the file matches the predicate, the file is selected for optimization\. Thus, to control the number of files affected by the compaction operation, you can specify a `WHERE` clause\.

### Configuring Compaction Properties<a name="querying-iceberg-data-optimization-configuring-compaction-properties"></a>

To control the size of the files to be selected for compaction and the resulting file size after compaction, you can use table property parameters\. You can use the [ALTER TABLE SET PROPERTIES](querying-iceberg-managing-tables.md#querying-iceberg-alter-table-set-properties) command to configure the related [table properties](querying-iceberg-creating-tables.md#querying-iceberg-table-properties)\.

### Example<a name="querying-iceberg-data-optimization-example"></a>

The following example merges delete files into data files and produces files near the targeted file size where the value of `catalog` is `c1`\.

```
OPTIMIZE iceberg_table REWRITE DATA
  USING BIN_PACK
  WHERE catalog = 'c1'
```

## Other Iceberg Table Optimizations<a name="querying-iceberg-data-optimization-other-actions"></a>

Iceberg offers other actions that you can perform to further optimize your Iceberg tables\. Among these are [snapshot expiration](https://iceberg.apache.org/docs/latest/spark-procedures/#expire_snapshots) and [orphan file removal](https://iceberg.apache.org/docs/latest/spark-procedures/#remove_orphan_files) for history and storage cleanup\. To perform these tasks, you can use [Amazon EMR](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-iceberg.html) or [Glue ETL](https://aws.amazon.com/marketplace/pp/prodview-iicxofvpqvsio)\. If you would like Athena to support a particular optimization operation, send feedback to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\.

**Warning**  
 If you run a snapshot expiration operation, you can no longer time travel to expired snapshots\. 