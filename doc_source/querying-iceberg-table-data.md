# Querying Iceberg table data and performing time travel<a name="querying-iceberg-table-data"></a>

To query an Iceberg dataset, use a standard `SELECT` statement like the following\. Queries follow the Apache Iceberg [format v2 spec](https://iceberg.apache.org/spec/#format-versioning) and perform merge\-on\-read of both position and equality deletes\.

```
SELECT * FROM [db_name.]table_name [WHERE predicate]
```

To optimize query times, all predicates are pushed down to where the data lives\.

## Time travel and version travel queries<a name="querying-iceberg-time-travel-and-version-travel-queries"></a>

Each Apache Iceberg table maintains a versioned manifest of the Amazon S3 objects that it contains\. Previous versions of the manifest can be used for time travel and version travel queries\.

Time travel queries in Athena query Amazon S3 for historical data from a consistent snapshot as of a specified date and time\. Version travel queries in Athena query Amazon S3 for historical data as of a specified snapshot ID\.

### Time travel queries<a name="querying-iceberg-time-travel-queries"></a>

To run a time travel query, use `FOR TIMESTAMP AS OF timestamp` after the table name in the `SELECT` statement, as in the following example\.

```
SELECT * FROM iceberg_table FOR TIMESTAMP AS OF timestamp
```

The system time to be specified for traveling is either a timestamp or timestamp with a time zone\. If not specified, Athena considers the value to be a timestamp in UTC time\.

The following example time travel queries select CloudTrail data for the specified date and time\.

```
SELECT * FROM iceberg_table FOR TIMESTAMP AS OF TIMESTAMP '2020-01-01 10:00:00 UTC'
```

```
SELECT * FROM iceberg_table FOR TIMESTAMP AS OF (current_timestamp – interval '1' day)
```

### Version travel queries<a name="querying-iceberg-version-travel-queries"></a>

To execute a version travel query \(that is, view a consistent snapshot as of a specified version\), use `FOR VERSION AS OF version` after the table name in the `SELECT` statement, as in the following example\.

```
SELECT * FROM [db_name.]table_name FOR VERSION AS OF version         
```

The *version* parameter is the `bigint` snapshot ID associated with an Iceberg table version\.

The following example version travel query selects data for the specified version\.

```
SELECT * FROM iceberg_table FOR VERSION AS OF 949530903748831860
```

**Note**  
The `FOR SYSTEM_TIME AS OF` and `FOR SYSTEM_VERSION AS OF` clauses in Athena engine version 2 have been replaced by the `FOR TIMESTAMP AS OF` and `FOR VERSION AS OF` clauses in Athena engine version 3\.

#### Retrieving the snapshot ID<a name="querying-iceberg-table-snapshot-id"></a>

You can use the SDKs provided by Iceberg in [Java](https://iceberg.apache.org/docs/latest/java-api-quickstart/) and [Python](https://iceberg.apache.org/docs/0.13.1/python-api-intro/#tables) to retrieve the Iceberg snapshot ID\. The following example is in Java\.

```
import org.apache.iceberg.Table;
import org.apache.iceberg.aws.glue.GlueCatalog;
import org.apache.iceberg.catalog.TableIdentifier;
import org.apache.iceberg.util.SnapshotUtil;

import java.text.SimpleDateFormat;
import java.util.Date;

Catalog catalog = new GlueCatalog();

Map<String, String> properties = new HashMap<String, String>();
properties.put("warehouse", "s3://my-bucket/my-folder");
catalog.initialize("my_catalog", properties);

Date date = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss").parse("2022/01/01 00:00:00");
long millis = date.getTime();

TableIdentifier name = TableIdentifier.of("db", "table");
Table table = catalog.loadTable(name);
long oldestSnapshotIdAfter2022 = SnapshotUtil.oldestAncestorAfter(table, millis);
```

### Combining time and version travel<a name="querying-iceberg-combining-time-and-version-travel"></a>

You can use time travel and version travel syntax in the same query to specify different timing and versioning conditions, as in the following example\.

```
SELECT table1.*, table2.* FROM 
  [db_name.]table_name FOR TIMESTAMP AS OF (current_timestamp - interval '1' day) AS table1 
  FULL JOIN 
  [db_name.]table_name FOR VERSION AS OF 5487432386996890161 AS table2 
  ON table1.ts = table2.ts 
  WHERE (table1.id IS NULL OR table2.id IS NULL)
```

## Creating and querying views with Iceberg tables<a name="querying-iceberg-views"></a>

To create and query Athena views on Iceberg tables, use `CREATE VIEW` views as described in [Working with views](views.md)\.

Example:

```
CREATE VIEW view1 AS SELECT * FROM iceberg_table
```

```
SELECT * FROM view1 
```

If you are interested in using the [Iceberg view specification](https://github.com/apache/iceberg/blob/master/format/view-spec.md) to create views, contact [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\. 

## Working with Lake Formation fine\-grained access control<a name="querying-iceberg-working-with-lf-fgac"></a>

Athena engine version 3 supports Lake Formation fine\-grained access control with Iceberg tables, including column level and row level security access control\. This access control works with time travel queries and with tables that have performed schema evolution\. For more information, see [Lake Formation fine\-grained access control and Athena workgroups](lf-athena-limitations.md#lf-athena-limitations-fine-grained-access-control)\.

If you created your Iceberg table outside of Athena, use [Apache Iceberg SDK](https://iceberg.apache.org/releases/) version 0\.13\.0 or higher so that your Iceberg table column information is populated in the AWS Glue Data Catalog\. If your Iceberg table does not contain column information in AWS Glue, you can use the Athena [ALTER TABLE SET PROPERTIES](querying-iceberg-managing-tables.md#querying-iceberg-alter-table-set-properties) statement or the latest Iceberg SDK to fix the table and update the column information in AWS Glue\. 