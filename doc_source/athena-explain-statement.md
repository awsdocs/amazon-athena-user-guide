# Using the EXPLAIN Statement in Athena<a name="athena-explain-statement"></a>

The `EXPLAIN` statement shows the logical or distributed execution plan of a specified SQL statement, or validates the SQL statement\. You can output the results in text format or in a data format for rendering into a graph\.

## Considerations and Limitations<a name="athena-explain-statement-considerations-and-limitations"></a>

The `EXPLAIN` statement in Athena has the following limitations\.
+ Because `EXPLAIN` queries do not scan any data, Athena does not charge for them\. However, because `EXPLAIN` queries make calls to AWS Glue to retrieve table metadata, you may incur charges from Glue if the calls go above the [free tier limit for Glue](http://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Categories=categories%23analytics&all-free-tier.q=glue&all-free-tier.q_operator=AND)\.
+ Currently, the `EXPLAIN` statement is not supported for the JDBC or ODBC drivers\.
+ Athena does not support `EXPLAIN ANALYZE`, which collects runtime statistics\.

## Syntax – Athena engine version 1<a name="athena-explain-statement-syntax-athena-engine-version-1"></a>

```
EXPLAIN [ ( option [, ...]) ] statement
```

*option* can be one of the following:

```
FORMAT { TEXT | GRAPHVIZ }
TYPE { LOGICAL | DISTRIBUTED | VALIDATE }
```

## Syntax – Athena engine version 2<a name="athena-explain-statement-syntax-athena-engine-version-2"></a>

```
EXPLAIN [ ( option [, ...]) ] statement
```

*option* can be one of the following:

```
FORMAT { TEXT | GRAPHVIZ | JSON }
TYPE { LOGICAL | DISTRIBUTED | VALIDATE | IO }
```

The `IO` type provides information about the tables and schemas that the query reads\. `IO` is supported only in Athena engine version 2 and can be returned only in JSON format\.

## Examples<a name="athena-explain-statement-examples"></a>

The following examples progress from the more straightforward to the more complex\. Example results are in text format\.

### Example 1\. Use the EXPLAIN statement to show a text query plan<a name="athena-explain-statement-example-text-query-plan"></a>

```
EXPLAIN 
SELECT 
   request_timestamp, 
   elb_name, 
   request_ip 
FROM sampledb.elb_logs;
```

#### Results<a name="athena-explain-statement-example-text-query-plan-results"></a>

```
- Output[request_timestamp, elb_name, request_ip] => [[request_timestamp, elb_name, request_ip]]
    - RemoteExchange[GATHER] => [[request_timestamp, elb_name, request_ip]]
        - TableScan[awsdatacatalog:HiveTableHandle{schemaName=sampledb, tableName=elb_logs, 
analyzePartitionValues=Optional.empty}] => [[request_timestamp, elb_name, request_ip]]
                LAYOUT: sampledb.elb_logs
                request_ip := request_ip:string:2:REGULAR
                request_timestamp := request_timestamp:string:0:REGULAR
                elb_name := elb_name:string:1:REGULAR
```

### Example 2\. Use the EXPLAIN statement to graph a query plan<a name="athena-explain-statement-example-graph-a-query-plan"></a>

```
EXPLAIN (FORMAT GRAPHVIZ)
SELECT 
      c.c_custkey,
      o.o_orderkey,
      o.o_orderstatus
   FROM tpch100.customer c 
   JOIN tpch100.orders o 
       ON c.c_custkey = o.o_custkey 
   WHERE c.c_custkey = 5566684
```

#### Results<a name="athena-explain-statement-example-graph-a-query-plan-results"></a>

```
Query Plan
digraph logical_plan {
subgraph cluster_graphviz_plan {
label = "SINGLE"
plannode_1[label="{Output[c_custkey, o_orderkey, o_orderstatus]}", style="rounded, filled", shape=record, fillcolor=white];
plannode_2[label="{ExchangeNode[GATHER]|\"c_custkey\", \"o_orderstatus\", \"o_orderkey\"}", style="rounded, filled", shape=record, fillcolor=gold];
plannode_3[label="{InnerJoin}", style="rounded, filled", shape=record, fillcolor=orange];
plannode_4[label="{Filter|(\"c_custkey\" = 5566684)}", style="rounded, filled", shape=record, fillcolor=yellow];
plannode_5[label="{TableScan[awsdatacatalog:HiveTableHandle\{schemaName=tpch100, tableName=customer, analyzePartitionValues=Optional.empty\}]}", style="rounded, filled", shape=record, fillcolor=deepskyblue];
plannode_6[label="{ExchangeNode[GATHER]|\"o_orderstatus\", \"o_orderkey\"}", style="rounded, filled", shape=record, fillcolor=gold];
plannode_7[label="{ExchangeNode[REPLICATE]|\"o_orderstatus\", \"o_orderkey\"}", style="rounded, filled", shape=record, fillcolor=gold];
plannode_8[label="{Project}", style="rounded, filled", shape=record, fillcolor=bisque];
plannode_9[label="{Filter|(\"o_custkey\" = 5566684)}", style="rounded, filled", shape=record, fillcolor=yellow];
plannode_10[label="{TableScan[awsdatacatalog:HiveTableHandle\{schemaName=tpch100, tableName=orders, analyzePartitionValues=Optional.empty\}]}", style="rounded, filled", shape=record, fillcolor=deepskyblue];
}
plannode_1, plannode_2;
plannode_2, plannode_3;
plannode_3, plannode_4;
plannode_4, plannode_5;
plannode_3, plannode_6;
plannode_6, plannode_7;
plannode_7, plannode_8;
plannode_8, plannode_9;
plannode_9, plannode_10;
}
```

To see the query plan visually, use the open source [Graphviz](https://graphviz.org/) tool to render all of the text in the results after `Query Plan` into a graph like the following\.

![\[Graph of the query plan rendered by the Graphviz tool.\]](http://docs.aws.amazon.com/athena/latest/ug/images/athena-explain-statement-1.png)

### Example 3\. Use the EXPLAIN statement to verify partition pruning<a name="athena-explain-statement-example-verify-partition-pruning"></a>

When you use a filtering predicate on a partitioned key to query a partitioned table, the query engine applies the predicate to the partitioned key to reduce the amount of data read\.

The following example uses an `EXPLAIN` query to verify partition pruning for a `SELECT` query on a partitioned table\. First, a `CREATE TABLE` statement creates the `tpch100.orders_partitioned` table\. The table is partitioned on column `o_orderdate`\.

```
CREATE TABLE `tpch100.orders_partitioned`(
  `o_orderkey` int, 
  `o_custkey` int, 
  `o_orderstatus` string, 
  `o_totalprice` double, 
  `o_orderpriority` string, 
  `o_clerk` string, 
  `o_shippriority` int, 
  `o_comment` string)
PARTITIONED BY ( 
  `o_orderdate` string)
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://<your_s3_bucket>/<your_directory_path>/'
```

The `tpch100.orders_partitioned` table has several partitions on `o_orderdate`, as shown by the `SHOW PARTITIONS` command\.

```
SHOW PARTITIONS tpch100.orders_partitioned;

o_orderdate=1994
o_orderdate=2015
o_orderdate=1998
o_orderdate=1995
o_orderdate=1993
o_orderdate=1997
o_orderdate=1992
o_orderdate=1996
```

The following `EXPLAIN` query verifies partition pruning on the specified `SELECT` statement\.

```
EXPLAIN 
SELECT 
   o_orderkey, 
   o_custkey, 
   o_orderdate 
FROM tpch100.orders_partitioned
WHERE o_orderdate = '1995'
```

#### Results<a name="athena-explain-statement-example-verify-partition-pruning-results"></a>

```
Query Plan
- Output[o_orderkey, o_custkey, o_orderdate] => [[o_orderkey, o_custkey, o_orderdate]]
    - RemoteExchange[GATHER] => [[o_orderkey, o_custkey, o_orderdate]]
        - TableScan[awsdatacatalog:HiveTableHandle{schemaName=tpch100, tableName=orders_partitioned, 
analyzePartitionValues=Optional.empty}] => [[o_orderkey, o_custkey, o_orderdate]]
                LAYOUT: tpch100.orders_partitioned
                o_orderdate := o_orderdate:string:-1:PARTITION_KEY
                    :: [[1995]]
                o_custkey := o_custkey:int:1:REGULAR
                o_orderkey := o_orderkey:int:0:REGULAR
```

The bold text in the result shows that the predicate `o_orderdate = '1995'` was applied on the `PARTITION_KEY`\.

### Example 4\. Use an EXPLAIN query to check the join order and join type<a name="athena-explain-statement-example-check-join-order-and-type"></a>

The following `EXPLAIN` query checks the `SELECT` statement's join order and join type\. Use a query like this to examine query memory usage so that you can reduce the chances of getting an `EXCEEDED_LOCAL_MEMORY_LIMIT` error\.

```
EXPLAIN (TYPE DISTRIBUTED)
   SELECT 
      c.c_custkey, 
      o.o_orderkey,
      o.o_orderstatus
   FROM tpch100.customer c 
   JOIN tpch100.orders o 
       ON c.c_custkey = o.o_custkey 
   WHERE c.c_custkey = 123
```

#### Results<a name="athena-explain-statement-example-check-join-order-and-type-results"></a>

```
Query Plan
Fragment 0 [SINGLE]
    Output layout: [c_custkey, o_orderkey, o_orderstatus]
    Output partitioning: SINGLE []
    Stage Execution Strategy: UNGROUPED_EXECUTION
    - Output[c_custkey, o_orderkey, o_orderstatus] => [[c_custkey, o_orderkey, o_orderstatus]]
        - RemoteSource[1] => [[c_custkey, o_orderstatus, o_orderkey]]

Fragment 1 [SOURCE]
    Output layout: [c_custkey, o_orderstatus, o_orderkey]
    Output partitioning: SINGLE []
    Stage Execution Strategy: UNGROUPED_EXECUTION
    - CrossJoin => [[c_custkey, o_orderstatus, o_orderkey]]
            Distribution: REPLICATED
        - ScanFilter[table = awsdatacatalog:HiveTableHandle{schemaName=tpch100, 
tableName=customer, analyzePartitionValues=Optional.empty}, grouped = false, 
filterPredicate = ("c_custkey" = 123)] => [[c_custkey]]
                LAYOUT: tpch100.customer
                c_custkey := c_custkey:int:0:REGULAR
        - LocalExchange[SINGLE] () => [[o_orderstatus, o_orderkey]]
            - RemoteSource[2] => [[o_orderstatus, o_orderkey]]

Fragment 2 [SOURCE]
    Output layout: [o_orderstatus, o_orderkey]
    Output partitioning: BROADCAST []
    Stage Execution Strategy: UNGROUPED_EXECUTION
    - ScanFilterProject[table = awsdatacatalog:HiveTableHandle{schemaName=tpch100, 
tableName=orders, analyzePartitionValues=Optional.empty}, grouped = false, 
filterPredicate = ("o_custkey" = 123)] => [[o_orderstatus, o_orderkey]]
            LAYOUT: tpch100.orders
            o_orderstatus := o_orderstatus:string:2:REGULAR
            o_custkey := o_custkey:int:1:REGULAR
            o_orderkey := o_orderkey:int:0:REGULAR
```

The example query was optimized into a cross join for better performance\. The results show that `tpch100.orders` will be distributed as the `BROADCAST` distribution type\. This implies that the `tpch100.orders` table will be distributed to all nodes that perform the join operation\. The `BROADCAST` distribution type will require that the all of the filtered results of the `tpch100.orders` table fit into the memory of each node that performs the join operation\.

However, the `tpch100.customer` table is smaller than `tpch100.orders`\. Because `tpch100.customer` requires less memory, you can rewrite the query to `BROADCAST tpch100.customer` instead of `tpch100.orders`\. This reduces the chance of the query receiving the `EXCEEDED_LOCAL_MEMORY_LIMIT` error\. This strategy assumes the following points:
+ The `tpch100.customer.c_custkey` is unique in the `tpch100.customer` table\.
+ There is a one\-to\-many mapping relationship between `tpch100.customer` and `tpch100.orders`\.

The following example shows the rewritten query\.

```
SELECT 
    c.c_custkey,
    o.o_orderkey,
    o.o_orderstatus
FROM tpch100.orders o
JOIN tpch100.customer c -- the filtered results of tpch100.customer are distributed to all nodes.
    ON c.c_custkey = o.o_custkey 
WHERE c.c_custkey = 123
```

### Example 5\. Use an EXPLAIN query to remove predicates that have no effect<a name="athena-explain-statement-example-remove-unneeded-predicates"></a>

You can use an `EXPLAIN` query to check the effectiveness of filtering predicates\. You can use the results to remove predicates that have no effect, as in the following example\.

```
EXPLAIN
   SELECT 
      c.c_name
   FROM tpch100.customer c
   WHERE c.c_custkey = CAST(RANDOM() * 1000 AS INT)
   AND c.c_custkey BETWEEN 1000 AND 2000
   AND c.c_custkey = 1500
```

#### Results<a name="athena-explain-statement-example-remove-unneeded-predicates-results"></a>

```
Query Plan
- Output[c_name] => [[c_name]]
    - RemoteExchange[GATHER] => [[c_name]]
        - ScanFilterProject[table = 
awsdatacatalog:HiveTableHandle{schemaName=tpch100, 
tableName=customer, analyzePartitionValues=Optional.empty}, 
filterPredicate = (("c_custkey" = 1500) AND ("c_custkey" = 
CAST(("random"() * 1E3) AS int)))] => [[c_name]]
                LAYOUT: tpch100.customer
                c_custkey := c_custkey:int:0:REGULAR
                c_name := c_name:string:1:REGULAR
```

The `filterPredicate` in the results shows that the optimizer merged the original three predicates into two predicates and changed their order of application\.

```
filterPredicate = (("c_custkey" = 1500) AND ("c_custkey" = CAST(("random"() * 1E3) AS int)))
```

Because the results show that the predicate `AND c.c_custkey BETWEEN 1000 AND 2000` has no effect, you can remove this predicate without changing the query results\.

For information about the terms used in the results of `EXPLAIN` queries, see [Understanding Athena EXPLAIN Statement Results](athena-explain-statement-understanding.md)\.

## Additional Resources<a name="athena-explain-statement-additional-resources"></a>

For additional information about `EXPLAIN` queries, see the following resources\.
+ Presto 0\.172 [https://prestodb.io/docs/0.172/sql/explain.html](https://prestodb.io/docs/0.172/sql/explain.html) documentation
+ Presto 0\.217 [https://prestodb.io/docs/0.217/sql/explain.html](https://prestodb.io/docs/0.217/sql/explain.html) documentation
+ [Explain the `EXPLAIN`](https://youtu.be/GcS02yTNwC0?t=1222) video on YouTube \(20:18\)