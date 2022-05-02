# Creating databases and tables<a name="work-with-data"></a>

Amazon Athena supports a subset of data definition language \(DDL\) statements and ANSI SQL functions and operators to define and query external tables where data resides in Amazon Simple Storage Service\. 

When you create a database and table in Athena, you describe the schema and the location of the data, making the data in the table ready for real\-time querying\. 

To improve query performance and reduce costs, we recommend that you partition your data and use open source columnar formats for storage in Amazon S3, such as [Apache parquet](https://parquet.apache.org) or [ORC](https://orc.apache.org/)\. 

**Topics**
+ [Creating databases](creating-databases.md)
+ [Creating tables](creating-tables.md)
+ [Names for tables, databases, and columns](tables-databases-columns-names.md)
+ [Reserved keywords](reserved-words.md)
+ [Table location in Amazon S3](tables-location-format.md)
+ [Columnar storage formats](columnar-storage.md)
+ [Converting to columnar formats](convert-to-columnar.md)
+ [Partitioning data](partitions.md)
+ [Partition projection](partition-projection.md)