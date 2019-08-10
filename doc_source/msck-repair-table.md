# MSCK REPAIR TABLE<a name="msck-repair-table"></a>

Recovers partitions and data associated with partitions\. Use this statement after you create a table for existing partitioned data, or have made significant changes to the data of a partitioned table\.

It is possible it will take some time to add all partitions\. If this operation times out, it will be in an incomplete state where only a few partitions are added to the catalog\. You should run the statement on the same table until all partitions are added\. Alternatively you can add each partition manually with [`ALTER TABLE ADD PARTITION`](alter-table-add-partition.md) to add each partition manually\.

`MSCK REPAIR TABLE` looks for path components with a partition key name and value separated by an equal sign, e.g. `year=2019/month=08/day=10`. If your path layout does not follow this pattern you need to use [`ALTER TABLE ADD PARTITION`](alter-table-add-partition.md) to add each partition manually\.

For more information, see [Partitioning Data](partitions.md)\.

## Synopsis<a name="synopsis"></a>

```
MSCK REPAIR TABLE table_name
```

## Examples<a name="examples"></a>

```
MSCK REPAIR TABLE orders;
```
