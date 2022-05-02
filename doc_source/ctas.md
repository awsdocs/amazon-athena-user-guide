# Creating a table from query results \(CTAS\)<a name="ctas"></a>

A `CREATE TABLE AS SELECT` \(CTAS\) query creates a new table in Athena from the results of a `SELECT` statement from another query\. Athena stores data files created by the CTAS statement in a specified location in Amazon S3\. For syntax, see [CREATE TABLE AS](create-table-as.md)\.

Use CTAS queries to: 
+ Create tables from query results in one step, without repeatedly querying raw data sets\. This makes it easier to work with raw data sets\.
+ Transform query results into other storage formats, such as Parquet and ORC\. This improves query performance and reduces query costs in Athena\. For information, see [Columnar storage formats](columnar-storage.md)\.
+ Create copies of existing tables that contain only the data you need\.

**Topics**
+ [Considerations and limitations for CTAS queries](considerations-ctas.md)
+ [Running CTAS queries in the console](ctas-console.md)
+ [Bucketing vs partitioning](bucketing-vs-partitioning.md)
+ [Examples of CTAS queries](ctas-examples.md)
+ [Using CTAS and INSERT INTO for ETL](ctas-insert-into-etl.md)
+ [Creating a table with more than 100 partitions](ctas-insert-into.md)