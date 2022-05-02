# November 26, 2021<a name="release-note-2021-11-26"></a>

Published on 2021\-11\-26

Athena announces the public preview of Athena ACID transactions, which add write, delete, update, and time travel operations to Athena's SQL data manipulation language \(DML\)\. Athena ACID transactions enable multiple concurrent users to make reliable, row\-level modifications to Amazon S3 data\. Built on the [Apache Iceberg](https://iceberg.apache.org) table format, Athena ACID transactions are compatible with other services and engines such as [Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html) and [Apache Spark](https://spark.apache.org/) that also support the Iceberg table formats\.

Athena ACID transactions and familiar SQL syntax simplify updates to your business and regulatory data\. For example, to respond to a data erasure request, you can perform a SQL `DELETE` operation\. To make manual record corrections, you can use a single `UPDATE` statement\. To recover data that was recently deleted, you can issue time travel queries using a `SELECT` statement\. Athena transactions are available through Athena's console, API operations, and ODBC and JDBC drivers\.

For more information, see [Using Athena ACID transactions](acid-transactions.md)\.