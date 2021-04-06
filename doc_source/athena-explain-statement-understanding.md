# Understanding Athena EXPLAIN Statement Results<a name="athena-explain-statement-understanding"></a>

This topic provides a brief guide to the operational terms used in Athena `EXPLAIN` statement results\.

## EXPLAIN Statement Output Types<a name="athena-explain-statement-understanding-explain-plan-types"></a>

`EXPLAIN` statement outputs can be one of two types:
+ **Logical Plan** – Shows the logical plan that the SQL engine uses to execute a statement\. The syntax for this option is `EXPLAIN` or `EXPLAIN (TYPE LOGICAL)`\.
+ **Distributed Plan** – Shows an execution plan in a distributed environment\. The output shows fragments, which are processing stages\. Each plan fragment is processed by one or more nodes\. Data can be exchanged between the nodes that process the fragments\. The syntax for this option is `EXPLAIN (TYPE DISTRIBUTED)`\.

  In the output for a distributed plan, fragments \(processing stages\) are indicated by `Fragment` *number* \[*fragment\_type*\], where *number* is a zero\-based integer and *fragment\_type* specifies how the fragment is executed by the nodes\. Fragment types, which provide insight into the layout of the data exchange, are described in the following table\.  
**Distributed Plan Fragment Types**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/athena-explain-statement-understanding.html)

## Exchange<a name="athena-explain-statement-understanding-exchange-types"></a>

Exchange\-related terms describe how data is exchanged between worker nodes\. Transfers can be either local or remote\. 

**LocalExchange \[*exchange\_type*\] **  
Transfers data locally within worker nodes for different stages of a query\. The value for *exchange\_type* can be one of the logical or distributed exchange types as described later in this section\.

**RemoteExchange \[*exchange\_type*\] **  
Transfers data between worker nodes for different stages of a query\. The value for *exchange\_type* can be one of the logical or distributed exchange types as described later in this section\.

### Logical Exchange Types<a name="athena-explain-statement-understanding-exchange-types-logical"></a>

The following exchange types describe actions taken during the exchange phase of a logical plan\.
+ **`GATHER`** – A single worker node gathers output from all other worker nodes\. For example, the last stage of a select query gathers results from all nodes and writes the results to Amazon S3\.
+ **`REPARTITION`** – Sends the row data to a specific worker based on the partitioning scheme required to apply to the next operator\.
+ **`REPLICATE`** – Copies the row data to all workers\.

### Distributed Exchange Types<a name="athena-explain-statement-understanding-exchange-types-distributed"></a>

The following exchange types indicate the layout of the data when they are exchanged between nodes in a distributed plan\.
+ **`HASH`** – The exchange distributes data to multiple destinations using a hash function\.
+ **`SINGLE`** – The exchange distributes data to a single destination\.

## Scanning<a name="athena-explain-statement-understanding-scanning"></a>

The following terms describe how data is scanned during a query\.

**TableScan **  
Scans a table's source data from Amazon S3 or an Apache Hive connector and applies partition pruning generated from the filter predicate\.

**ScanFilter **  
Scans a table's source data from Amazon S3 or a Hive connector and applies partition pruning generated from the filter predicate and from additional filter predicates not applied through partition pruning\.

**ScanFilterProject **  
First, scans a table's source data from Amazon S3 or a Hive connector and applies partition pruning generated from the filter predicate and from additional filter predicates not applied through partition pruning\. Then, modifies the memory layout of the output data into a new projection to improve performance of later stages\.

## Join<a name="athena-explain-statement-understanding-join"></a>

Joins data between two tables\. Joins can be categorized by join type and by distribution type\.

### Join Types<a name="athena-explain-statement-understanding-join-types"></a>

Join types define the way in which the join operation occurs\.

**CrossJoin** – Produces the Cartesian product of the two tables joined\.

**InnerJoin** – Selects records that have matching values in both tables\.

**LeftJoin** – Selects all records from the left table and the matching records from the right table\. If no match occurs, the result on the right side is NULL\.

**RightJoin** – Selects all records from the right table, and the matching records from the left table\. If no match occurs, the result on the left side is NULL\.

**FullJoin** – Selects all records where there is a match in the left or right table records\. The joined table contains all records from both the tables and fills in NULLs for missing matches on either side\.

**Note**  
For performance reasons, the query engine can rewrite a join query into a different join type to produce the same results\. For example, an inner join query with predicate on one table can be rewritten into a `CrossJoin`\. This pushes the predicate down to the scanning phase of the table so that fewer data are scanned\.

### Join Distribution Types<a name="athena-explain-statement-understanding-join-distribution-types"></a>

Distribution types define how data is exchanged between worker nodes when the join operation is performed\.

**Partitioned** – Both the left and right table are hash\-partitioned across all worker nodes\. Partitioned distribution consumes less memory in each node\. Partitioned distribution can be much slower than replicated joins\. Partitioned joins are suitable when you join two large tables\.

**Replicated** – One table is hash\-partitioned across all worker nodes and the other table is replicated to all worker nodes to perform the join operation\. Replicated distribution can be much faster than partitioned joins, but it consumes more memory in each worker node\. If the replicated table is too large, the worker node can experience an out\-of\-memory error\. Replicated joins are suitable when one of the joined tables is small\.