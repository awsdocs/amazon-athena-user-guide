# Other Athena Operations on Iceberg Tables<a name="querying-iceberg-additional-operations"></a>

## Database level operations<a name="querying-iceberg-additional-operations-database-level-operations"></a>

When you use [DROP DATABASE](drop-database.md) with the `CASCADE` option , any Iceberg table data is also removed\. The following DDL operations have no effect on Iceberg tables\.
+ [CREATE DATABASE](create-database.md)
+ [ALTER DATABASE SET DBPROPERTIES](alter-database-set-dbproperties.md)
+ [SHOW DATABASES](show-databases.md)
+ [SHOW TABLES](show-tables.md)
+ [SHOW VIEWS](show-views.md)

## Partition related operations<a name="querying-iceberg-additional-operations-partition-related-operations"></a>

Because Iceberg tables use [hidden partitioning](https://iceberg.apache.org/docs/latest/partitioning/#icebergs-hidden-partitioning), you do not have to work with physical partitions directly\. As a result, Iceberg tables in Athena do not support the following partition\-related DDL operations:
+ [SHOW PARTITIONS](show-partitions.md)
+ [ALTER TABLE ADD PARTITION](alter-table-add-partition.md)
+ [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md)
+ [ALTER TABLE RENAME PARTITION](alter-table-rename-partition.md)

If you would like to see Iceberg [ partition evolution](https://iceberg.apache.org/docs/latest/evolution/#partition-evolution) in Athena, send feedback to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\.

## Unloading Iceberg tables<a name="querying-iceberg-additional-operations-unload-iceberg-table"></a>

Iceberg tables can be unloaded to files in a folder on Amazon S3\. For information, see [UNLOAD](unload.md)\.

## MSCK REPAIR<a name="querying-iceberg-additional-operations-msck-repair"></a>

Because Iceberg tables keep track of table layout information, running [MSCK REPAIR TABLE](msck-repair-table.md) as one does with Hive tables is not necessary and is not supported\.