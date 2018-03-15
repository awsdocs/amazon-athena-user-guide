# Working with Source Data<a name="work-with-data"></a>

Amazon Athena supports a subset of data definition language \(DDL\) statements and ANSI SQL functions and operators to define and query external tables where data resides in Amazon Simple Storage Service\. 

When you create a database and table in Athena, you describe the schema and the location of the data, making the data in the table ready for read\-time querying\. 

To improve query performance and reduce costs, we recommend that you partition your data and use open source columnar formats for storage in Amazon S3, such as [Apache Parquet](https://parquet.apache.org) or [ORC](https://orc.apache.org/)\. 


+ [Tables and Databases Creation Process in Athena](creating-tables.md)
+ [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)
+ [Table Location in Amazon S3](tables-location-format.md)
+ [Partitioning Data](partitions.md)
+ [Converting to Columnar Formats](convert-to-columnar.md)