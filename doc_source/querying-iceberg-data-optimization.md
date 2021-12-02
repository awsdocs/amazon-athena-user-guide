# Optimizing Iceberg Tables<a name="querying-iceberg-data-optimization"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

As data accumulates into an Iceberg table, queries gradually become less efficient because of the increased processing time required to open files\. Additional computational cost is incurred if the table contains [delete files](https://iceberg.apache.org/#spec/#row-level-deletes)\. In Iceberg, delete files store row\-level deletes, and the engine must apply the deleted rows to query results\.

In this preview, to help optimize the performance of queries on Iceberg tables, Athena supports manual compaction as a table maintenance command\. Compactions optimize the structural layout of the table without altering table content\.

## REWRITE DATA Compaction Action<a name="querying-iceberg-data-optimization-rewrite-data-action"></a>

The `REWRITE DATA` compaction action rewrites data files into a more optimized layout based on their size and number of associated delete files\.

### Syntax<a name="querying-iceberg-data-optimization-rewrite-data-action-syntax"></a>

The following syntax summary shows how to optimize data layout for an Iceberg table\.

```
OPTIMIZE table_name REWRITE DATA
  USING BIN_PACK
  [WHERE predicate]
```

The `REWRITE DATA` action uses predicates to select for files that contain matching rows\. If any row in the file matches the predicate, the file is selected for optimization\. Thus, to control the number of files affected by the compaction operation, you can specify a `WHERE` clause\.

## Configuring Compaction Properties<a name="querying-iceberg-data-optimization-configuring-compaction-properties"></a>

To control the size of the files to be selected for compaction and the resulting file size after compaction, you can use table property parameters\. You can use the [ALTER TABLE SET PROPERTIES](querying-iceberg-ddl-operations.md#querying-iceberg-alter-table-set-properties) command to configure the following table properties:
+ **compaction\_bin\_pack\_target\_file\_size\_bytes** – The approximate target file size of the output files after they are rewritten\. The default size is 536870912 \(512 MB\)\.
+ **compaction\_bin\_pack\_min\_file\_size\_bytes** – Files smaller than the specified value are included for compaction\. Must be less than 10GB and less than the value of `compaction_bin_pack_target_file_size_bytes`\. The default size is 0\.75 times the value specified by `compaction_bin_pack_target_file_size_bytes`\.
+ **compaction\_bin\_pack\_max\_file\_size\_bytes** – Files larger than the specified value are included for compaction\. The specified size must be less than 10GB and greater than the value of `compaction_bin_pack_target_file_size_bytes`\. The default size is 1\.8 times the value specified by `compaction_bin_pack_target_file_size_bytes`\.

## Example<a name="querying-iceberg-data-optimization-example"></a>

The following example merges delete files into data files and produces files near the targeted file size where the value of `catalog` is `c1`\.

```
OPTIMIZE table REWRITE DATA
  USING BIN_PACK
  WHERE catalog = 'c1'
```