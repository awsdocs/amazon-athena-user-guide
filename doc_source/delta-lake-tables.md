# Querying Delta Lake tables<a name="delta-lake-tables"></a>

[Delta Lake](https://delta.io/) is a table format for big data analytics\. You can use Amazon Athena to read Delta Lake tables stored in Amazon S3 directly without having to generate manifest files or run the `MSCK REPAIR` statement\.

The Delta Lake format stores the minimum and maximum values per column of each data file\. The Athena implementation makes use of this information to enable file\-skipping on predicates to eliminate unwanted files from consideration\.

## Considerations and limitations<a name="delta-lake-tables-considerations-and-limitations"></a>

Delta Lake support in Athena has the following limitations:
+ **Tables with AWS Glue catalog only** – Native Delta Lake support is supported only through tables registered with AWS Glue\. If you have a Delta Lake table that is registered with another metastore, you can still keep it and treat it as your primary metastore\. Because Delta Lake metadata is stored in the file system \(for example, in Amazon S3\) rather than in the metastore, Athena requires only the location property in AWS Glue to read from your Delta Lake tables\.
+ **V3 engine only** – Delta Lake queries are supported only on Athena engine version 3\. You must ensure that the workgroup you create is configured to use Athena engine version 3\.
+ **No time travel support** – There is no support for queries that use Delta Lake’s time travel capabilities\.
+ **Read only** – Write DML statements like `UPDATE`, `INSERT`, or `DELETE` are not supported\.
+ **No Lake Formation integration** – Lake Formation integration is not supported for Delta Lake tables\.
+ **Limited DDL support** – The following DDL statements are supported: `CREATE EXTERNAL TABLE`, `SHOW COLUMNS`, `SHOW TBLPROPERTIES`, `SHOW PARTITIONS`, `SHOW CREATE TABLE`, and `DESCRIBE TABLE`\. For information on using the `CREATE EXTERNAL TABLE` statement, see the [Getting started](#delta-lake-tables-getting-started) section\.

## Supported non\-partition column data types<a name="delta-lake-tables-supported-data-types-non-partition-columns"></a>

For non\-partition columns, all data types that Athena supports except `CHAR` are supported \(`CHAR` is not supported in the Delta Lake protocol itself\)\. Supported data types include:

```
boolean
tinyint
smallint
integer
bigint
double
float
decimal
varchar
string
binary
date
timestamp
array
map
struct
```

## Supported partition column data types<a name="delta-lake-tables-supported-data-types-partition-columns"></a>

For partition columns, Athena supports tables with the following data types:

```
boolean
integer
smallint
tinyint
bigint
decimal
float
double
date
timestamp
varchar
```

For more information about the data types in Athena, see [Data types in Amazon Athena](data-types.md)\.

## Getting started<a name="delta-lake-tables-getting-started"></a>

To be queryable, your Delta Lake table must exist in AWS Glue\. If your table is in Amazon S3 but not in AWS Glue, run a `CREATE EXTERNAL TABLE` statement using the following syntax\. If your table already exists in AWS Glue \(for example, because you are using Apache Spark or another engine with AWS Glue\), you can skip this step\.

### <a name="delta-lake-tables-getting-started-create-table"></a>

```
CREATE EXTERNAL TABLE
  [db_name.]table_name
  LOCATION 's3://DOC-EXAMPLE-BUCKET/your-folder/'
  TBLPROPERTIES ('table_type' = 'DELTA')
```

Note the omission of column definitions, SerDe library, and other table properties\. Unlike traditional Hive tables, Delta Lake table metadata are inferred from the Delta Lake transaction log and synchronized directly to AWS Glue\.

**Note**  
For Delta Lake tables, `CREATE TABLE` statements that include more than the `LOCATION` and `table_type` property are not allowed\.

## Reading Delta Lake tables<a name="delta-lake-tables-querying"></a>

To query a Delta Lake table, use standard SQL `SELECT` syntax:

```
[ WITH with_query [, ...] ]SELECT [ ALL | DISTINCT ] select_expression [, ...]
[ FROM from_item [, ...] ]
[ WHERE condition ]
[ GROUP BY [ ALL | DISTINCT ] grouping_element [, ...] ]
[ HAVING condition ]
[ { UNION | INTERSECT | EXCEPT } [ ALL | DISTINCT ] select ]
[ ORDER BY expression [ ASC | DESC ] [ NULLS FIRST | NULLS LAST] [, ...] ]
[ OFFSET count [ ROW | ROWS ] ]
[ LIMIT [ count | ALL ] ]
```

For more information about `SELECT` syntax, see [SELECT](select.md) in the Athena documentation\.

The Delta Lake format stores the minimum and maximum values per column of each data file\. Athena makes use of this information to enable file skipping on predicates to eliminate unnecessary files from consideration\.

## Synchronizing Delta Lake metadata<a name="delta-lake-tables-syncing-metadata"></a>

Athena synchronizes table metadata, including schema, partition columns, and table properties, to AWS Glue if you use Athena to create your Delta Lake table\. As time passes, this metadata can lose its synchronization with the underlying table metadata in the transaction log\. To keep your table up to date, you can use the AWS Glue crawler for Delta Lake tables\. For more information, see [Introducing native Delta Lake table support with AWS Glue crawlers](http://aws.amazon.com/blogs/big-data/introducing-native-delta-lake-table-support-with-aws-glue-crawlers/) in the AWS Big Data Blog and [Scheduling an AWS Glue crawler](https://docs.aws.amazon.com/glue/latest/dg/schedule-crawler.html) in the AWS Glue Developer Guide\. 