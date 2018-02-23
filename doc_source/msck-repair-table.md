# MSCK REPAIR TABLE<a name="msck-repair-table"></a>

Recovers partitions and data associated with partitions\. Use this statement when you add partitions to the catalog\. It is possible it will take some time to add all partitions\. If this operation times out, it will be in an incomplete state where only a few partitions are added to the catalog\. You should run the statement on the same table until all partitions are added\. For more information, see [Partitioning Data](partitions.md)\.

## Synopsis<a name="synopsis"></a>

```
MSCK REPAIR TABLE table_name
```

## Examples<a name="examples"></a>

```
MSCK REPAIR TABLE orders;
```