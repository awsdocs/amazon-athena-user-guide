# Running SQL Queries Using Amazon Athena<a name="querying-athena-tables"></a>

You can run SQL queries using Amazon Athena on data sources that are registered with the AWS Glue Data Catalog and data sources that you connect to using Athena query federation \(preview\), such as Hive metastores and Amazon DocumentDB instances\. For more information about working with data sources, see [Connecting to Data Sources](work-with-data-stores.md)\. When you run a Data Definition Language \(DDL\) query that modifies schema, Athena writes the metadata to the metastore associated with the data source\. In addition, some queries, such as `CREATE TABLE AS` and `INSERT INTO` can write records to the dataset—for example, adding a CSV record to an Amazon S3 location\. When you run a query, Athena saves the results of a query in a query result location that you specify\. This allows you to view query history and to download and view query results sets\.

This section provides guidance for running Athena queries on common data sources and data types using a variety of SQL statements\. General guidance is provided for working with common structures and operators—for example, working with arrays, concatenating, filtering, flattening, and sorting\. Other examples include queries for data in tables with nested structures and maps, tables based on JSON\-encoded datasets, and datasets associated with AWS services such as AWS CloudTrail logs and Amazon EMR logs\.

**Topics**
+ [Considerations and Limitations](#querying-athena-tables-considerations-limitations)
+ [Query Results and Query History](querying.md)
+ [Working with Views](views.md)
+ [Creating a Table from Query Results \(CTAS\)](ctas.md)
+ [Handling Schema Updates](handling-schema-updates-chapter.md)
+ [Querying Arrays](querying-arrays.md)
+ [Querying JSON](querying-JSON.md)
+ [Querying Geospatial Data](querying-geospatial-data.md)
+ [Using ML with Athena \(Preview\)](querying-mlmodel.md)
+ [Querying with UDFs \(Preview\)](querying-udf.md)
+ [Querying AWS Service Logs](querying-AWS-service-logs.md)
+ [Querying AWS Glue Data Catalog](querying-glue-catalog.md)

## Considerations and Limitations<a name="querying-athena-tables-considerations-limitations"></a>

When querying tables in Athena, keep in mind the following considerations and limitations\.
+ When data is moved or transitioned to the [Amazon S3 GLACIER storage class](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html#sc-glacier), it is no longer readable or queryable by Athena\. This is true even after storage class objects are restored\. To make the restored objects that you want to query readable by Athena, copy the restored objects back into Amazon S3 to change their storage class\. 
+ You cannot use an Amazon S3 access point in a `LOCATION` clause\. However, as long the as the Amazon S3 bucket policy does not explicitly deny requests to objects not made through Amazon S3 access points, the objects should be accessible from Athena for requestors that have the right object access permissions\.
+ Athena treats sources files that start with an underscore \(`_`\) or a dot \(`.`\) as hidden\. To work around this limitation, rename the files\.