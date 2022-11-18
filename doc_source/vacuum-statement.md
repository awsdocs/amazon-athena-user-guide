# VACUUM<a name="vacuum-statement"></a>

The `VACUUM` statement performs table maintenance on Apache Iceberg tables by removing no longer needed data files\.

**Note**  
`VACUUM` is transactional and is supported only for Apache Iceberg tables in Athena engine version 3\.

## Synopsis<a name="vacuum-statement-synopsis"></a>

To remove data files no longer needed for an Iceberg table, use the following syntax\.

```
VACUUM target_table
```

Running the `VACUUM` statement on Iceberg tables is recommended to remove data files that are no longer relevant and to reduce metadata size and storage consumption\. 

**Warning**  
If you run a snapshot expiration operation, you can no longer time travel to expired snapshots\.

`VACUUM` performs the following operations:
+ Removes snapshots that are older than the amount of time that is specified by the `vacuum_max_snapshot_age_seconds` table property\. By default, this property is set to 432000 seconds \(5 days\)\.
+ Removes snapshots that are not within the period to be retained that are in excess of the number specified by the `vacuum_min_snapshots_to_keep` table property\. The default is 1\.

  You can specify these table properties in your `CREATE TABLE` statement\. After the table has been created, you can use the [ALTER TABLE SET PROPERTIES](querying-iceberg-managing-tables.md#querying-iceberg-alter-table-set-properties) statement to update them\. 
+ Removes any metadata and data files that are unreachable as a result of the snapshot removal\.
+ Removes orphan files that are older than the time specified in the `vacuum_max_snapshot_age_seconds` table property\. Orphan files are files in the table's data directory that are not part of the table state\.

For more information about creating and managing Apache Iceberg tables in Athena, see [Creating Iceberg tables](querying-iceberg-creating-tables.md) and [Managing Iceberg tables](querying-iceberg-managing-tables.md)\.