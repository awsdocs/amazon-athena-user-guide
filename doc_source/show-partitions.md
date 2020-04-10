# SHOW PARTITIONS<a name="show-partitions"></a>

Lists all the partitions in a table\.

## Synopsis<a name="synopsis"></a>

```
SHOW PARTITIONS table_name
```

To view the contents of a partition, use a `SELECT` query\. For more information, see the [Query the Data](partitions.md#query-the-data) section on the [Partitioning Data](partitions.md) page\.

## Examples<a name="examples"></a>

```
SHOW PARTITIONS clicks;
```