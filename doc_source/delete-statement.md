# DELETE<a name="delete-statement"></a>

Deletes rows in an Apache Iceberg table\. `DELETE` is transactional and is supported only for Apache Iceberg tables\.

## Synopsis<a name="delete-statement-synopsis"></a>

To delete the rows from an Iceberg table, use the following syntax\.

```
DELETE FROM [db_name.]table_name [WHERE predicate]
```

For more information and examples, see the `DELETE` section of [Updating Iceberg table data](querying-iceberg-updating-iceberg-table-data.md)\.