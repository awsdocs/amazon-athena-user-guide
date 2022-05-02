# Querying governed tables<a name="lf-governed-tables-querying"></a>

To query a governed dataset, use a standard `SELECT` statement like the following\.

```
SELECT * FROM databasename.tablename [WHERE predicate]
```

## Predicate pushdown support<a name="lf-governed-tables-querying-predicate-pushdown"></a>

To optimize query times, some predicates are pushed down to where the data lives\. The following tables summarize the Athena data types and logical operators currently supported in predicate pushdown\.


****  

| Athena data type | Pushed down | 
| --- | --- | 
| array | No | 
| bigint | Yes | 
| binary | No | 
| boolean | Yes | 
| char | No | 
| date | Yes | 
| decimal | Yes | 
| double | Yes | 
| float | Yes | 
| int, integer | Yes | 
| map | No | 
| smallint | Yes | 
| string | Yes | 
| struct | No | 
| timestamp | Yes | 
| tinyint | Yes | 
| varchar | Yes | 


****  

| Logical operator | Example query | Pushed down | 
| --- | --- | --- | 
| single or multiple AND | SELECT \* FROM table WHERE col\_1 = a AND col\_2 = b | Yes | 
| OR conditions on same column | SELECT \* FROM table WHERE col\_1 = a OR col\_1 = b | Yes | 
| OR conditions on different columns | SELECT \* FROM table WHERE col\_1 = a OR col\_2 = b | No | 
| IS NULL, IS NOT NULL | SELECT \* FROM table WHERE col\_1 IS NOT NULL | Yes | 
| NOT | SELECT \* FROM table WHERE NOT col\_1 = a | Yes | 
| IN | SELECT \* FROM table WHERE NOT col\_1 IN \(a, b\) | Yes | 
| BETWEEN | SELECT \* FROM table WHERE col\_1 BETWEEN A AND B | Yes | 
| LIKE | SELECT \* FROM table WHERE col\_1 LIKE 'expression%' | No | 

## Time travel and version travel queries<a name="lf-governed-tables-time-travel-and-version-travel-queries"></a>

Each governed table maintains a versioned manifest of the Amazon S3 objects that it comprises\. Previous versions of the manifest can be used for time travel and version travel queries\.

Time travel queries in Athena query Amazon S3 for historical data from a consistent snapshot as of a specified date and time\. Version travel queries in Athena query Amazon S3 for historical data as of a specified snapshot ID\.

### Time travel queries<a name="lf-governed-tables-example-syntax-time-travel"></a>

To run a time travel query, use `FOR SYSTEM_TIME AS OF timestamp` after the table name in the `SELECT` statement, as in the following example:

```
SELECT * FROM database.table FOR SYSTEM_TIME AS OF timestamp
```

The system time to be specified for traveling is either a timestamp or timestamp with a time zone\. If not specified, Athena considers the value to be a timestamp in UTC time\. 

The following example time travel queries select data for the specified date and time\.

```
SELECT * FROM governed_table 
FOR SYSTEM_TIME AS OF TIMESTAMP '2020-01-01 10:00:00'
```

```
SELECT * FROM governed_table 
FOR SYSTEM_TIME AS OF (current_timestamp - interval '1' day)
```

### Version travel queries<a name="lf-governed-tables-example-syntax-version-travel"></a>

To perform a version travel query \(that is, view a consistent snapshot as of a specified version\), use `FOR SYSTEM_VERSION AS OF version` after the table name in the `SELECT` statement, as in the following example\. 

```
SELECT * FROM database.table FOR SYSTEM_VERSION AS OF version
```

The *version* parameter is the `bigint` snapshot ID associated with a governed table version\.

The following example version travel query selects data for the specified version\.

```
SELECT * FROM governed_table FOR SYSTEM_VERSION AS OF 949530903748831860
```

### Combining time and version travel<a name="lf-governed-tables-combining-time-and-version-travel"></a>

You can use time travel and version travel syntax in the same query to specify different timing and versioning conditions, as in the following example\.

```
SELECT table1.*, table2.* FROM 
 governed_table FOR SYSTEM_TIME AS OF (current_timestamp - interval '1' day) AS table1 
 FULL JOIN 
 governed_table FOR SYSTEM_VERSION AS OF 5487432386996890161 AS table2 
 ON table1.ts = table2.ts 
 WHERE (table1.id IS NULL OR table2.id IS NULL)
```