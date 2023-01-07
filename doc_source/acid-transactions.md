# Using Athena ACID transactions<a name="acid-transactions"></a>

The term "ACID transactions" refers to a set of properties \([atomicity](https://en.wikipedia.org/wiki/Atomicity_(database_systems)), [consistency](https://en.wikipedia.org/wiki/Consistency_(database_systems)), [isolation](https://en.wikipedia.org/wiki/Isolation_(database_systems)), and [durability](https://en.wikipedia.org/wiki/Durability_(database_systems))\) that ensure data integrity in database transactions\. ACID transactions enable multiple users to concurrently and reliably add and delete Amazon S3 objects in an atomic manner, while isolating any existing queries by maintaining read consistency for queries against the data lake\. Athena ACID transactions add single\-table support for insert, delete, update, and time travel operations to the Athena SQL data manipulation language \(DML\)\. You and multiple concurrent users can use Athena ACID transactions to make reliable, row\-level modifications to Amazon S3 data\. Athena transactions automatically manage locking semantics and coordination and do not require a custom record locking solution\.

Athena ACID transactions and familiar SQL syntax simplify updates to your business and regulatory data\. For example, to respond to a data erasure request, you can perform a SQL `DELETE` operation\. To make manual record corrections, you can use a single `UPDATE` statement\. To recover data that was recently deleted, you can issue time travel queries using a `SELECT` statement\.

Because they are built on shared table formats, Athena ACID transactions are compatible with other services and engines such as [Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html) and [Apache Spark](https://spark.apache.org/) that also support shared table formats\.

Athena transactions are available through the Athena console, API operations, and ODBC and JDBC drivers\.

**Topics**
+ [Querying Delta Lake tables](delta-lake-tables.md)
+ [Using Athena to query Apache Hudi datasets](querying-hudi.md)
+ [Using Iceberg tables](querying-iceberg.md)