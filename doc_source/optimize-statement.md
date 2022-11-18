# OPTIMIZE<a name="optimize-statement"></a>

Optimizes rows in an Apache Iceberg table by rewriting data files into a more optimized layout based on their size and number of associated delete files\.

**Note**  
`OPTIMIZE` is transactional and is supported only for Apache Iceberg tables\.

## Syntax<a name="optimize-statement-syntax"></a>

The following syntax summary shows how to optimize data layout for an Iceberg table\.

```
OPTIMIZE [db_name.]table_name REWRITE DATA USING BIN_PACK
  [WHERE predicate]
```

The compaction action is charged by the amount of data scanned during the rewrite process\. The `REWRITE DATA` action uses predicates to select for files that contain matching rows\. If any row in the file matches the predicate, the file is selected for optimization\. Thus, to control the number of files affected by the compaction operation, you can specify a `WHERE` clause\.

## Configuring compaction properties<a name="optimize-statement-configuring-compaction-properties"></a>

To control the size of the files to be selected for compaction and the resulting file size after compaction, you can use table property parameters\. You can use the [ALTER TABLE SET PROPERTIES](querying-iceberg-managing-tables.md#querying-iceberg-alter-table-set-properties) command to configure the related [table properties](querying-iceberg-creating-tables.md#querying-iceberg-table-properties)\.

For more information and examples, see the `OPTIMIZE` section of [Updating Iceberg table data](querying-iceberg-updating-iceberg-table-data.md)\.