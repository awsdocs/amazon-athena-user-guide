# SHOW PARTITIONS<a name="show-partitions"></a>

Lists all the partitions in a table\.

## Synopsis<a name="synopsis"></a>

```
SHOW PARTITIONS table_name
```
+ To show the partitions in a table and list them in a specific order, see the [Listing Partitions for a Specific Table](querying-glue-catalog.md#querying-glue-catalog-listing-partitions) section on the [Querying AWS Glue Data Catalog](querying-glue-catalog.md) page\.
+ To view the contents of a partition, see the [Query the Data](partitions.md#query-the-data) section on the [Partitioning Data](partitions.md) page\.

## Examples<a name="examples"></a>

```
SHOW PARTITIONS clicks;
```