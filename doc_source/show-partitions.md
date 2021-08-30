# SHOW PARTITIONS<a name="show-partitions"></a>

Lists all the partitions in an Athena table in unsorted order\.

## Synopsis<a name="synopsis"></a>

```
SHOW PARTITIONS table_name
```
+ To show the partitions in a table and list them in a specific order, see the [Listing Partitions for a Specific Table](querying-glue-catalog.md#querying-glue-catalog-listing-partitions) section on the [Querying AWS Glue Data Catalog](querying-glue-catalog.md) page\.
+ To view the contents of a partition, see the [Query the Data](partitions.md#query-the-data) section on the [Partitioning Data in Athena](partitions.md) page\.
+ `SHOW PARTITIONS` does not list partitions that are projected by Athena but not registered in the AWS Glue catalog\. For information about partition projection, see [Partition Projection with Amazon Athena](partition-projection.md)\.
+  `SHOW PARTITIONS` lists the partitions in metadata, not the partitions in the actual file system\. To update the metadata after you delete partitions manually in Amazon S3, run [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md)\. 

## Examples<a name="examples"></a>

The following example query shows the partitions for the `flight_delays_csv` table, which shows flight table data from the US Department of Transportation\. For more information about the example `flight_delays_csv` table, see [LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files](lazy-simple-serde.md)\. The table is partitioned by year\.

```
SHOW PARTITIONS flight_delays_csv
```

**Results**

```
year=2007
year=2015
year=1999
year=1993
year=1991
year=2003
year=1996
year=2014
year=2004
year=2011
...
```

The following example query shows the partitions for the `impressions` table, which contains sample web browsing data\. For more information about the example `impressions` table, see [Partitioning Data in Athena](partitions.md)\. The table is partitioned by the `dt` \(datetime\) column\.

```
SHOW PARTITIONS impressions
```

**Results**

```
dt=2009-04-12-16-00
dt=2009-04-13-18-15
dt=2009-04-14-00-20
dt=2009-04-12-13-00
dt=2009-04-13-02-15
dt=2009-04-14-12-05
dt=2009-04-14-06-15
dt=2009-04-12-21-15
dt=2009-04-13-22-15
...
```

### Listing Partitions in Sorted Order<a name="show-partitions-examples-ordering"></a>

To order the partitions in the results list in Athena engine version 2, use the following `SELECT` syntax instead of `SHOW PARTITIONS`\.

```
SELECT * FROM "table_name$partitions" ORDER BY column_name
```

The following query shows the list of partitions for the `flight_delays_csv` example, but in sorted order\.

```
SELECT * FROM "flight_delays_csv$partitions" ORDER BY year
```

**Results**

```
year
1987
1988
1989
1990
1991
1992
1993
1994
1995
1996
1997
1998
1999
...
```

For more information, see the [Listing Partitions for a Specific Table](querying-glue-catalog.md#querying-glue-catalog-listing-partitions) section on the [Querying AWS Glue Data Catalog](querying-glue-catalog.md) page\.