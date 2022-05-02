# Running SQL queries using Amazon Athena<a name="querying-athena-tables"></a>

You can run SQL queries using Amazon Athena on data sources that are registered with the AWS Glue Data Catalog and data sources such as Hive metastores and Amazon DocumentDB instances that you connect to using the Athena Federated Query feature\. For more information about working with data sources, see [Connecting to data sources](work-with-data-stores.md)\. When you run a Data Definition Language \(DDL\) query that modifies schema, Athena writes the metadata to the metastore associated with the data source\. In addition, some queries, such as `CREATE TABLE AS` and `INSERT INTO` can write records to the dataset—for example, adding a CSV record to an Amazon S3 location\. When you run a query, Athena saves the results of a query in a query result location that you specify\. This allows you to view query history and to download and view query results sets\.

This section provides guidance for running Athena queries on common data sources and data types using a variety of SQL statements\. General guidance is provided for working with common structures and operators—for example, working with arrays, concatenating, filtering, flattening, and sorting\. Other examples include queries for data in tables with nested structures and maps, tables based on JSON\-encoded datasets, and datasets associated with AWS services such as AWS CloudTrail logs and Amazon EMR logs\. Comprehensive coverage of standard SQL usage is beyond the scope of this documentation\.

**Topics**
+ [Query results and recent queries](querying.md)
+ [Working with views](views.md)
+ [Using saved queries](saved-queries.md)
+ [Querying with prepared statements](querying-with-prepared-statements.md)
+ [Handling schema updates](handling-schema-updates-chapter.md)
+ [Querying arrays](querying-arrays.md)
+ [Querying geospatial data](querying-geospatial-data.md)
+ [Querying Hudi datasets](querying-hudi.md)
+ [Querying JSON](querying-JSON.md)
+ [Using ML with Athena](querying-mlmodel.md)
+ [Querying with UDFs](querying-udf.md)
+ [Querying across regions](querying-across-regions.md)
+ [Querying AWS Glue Data Catalog](querying-glue-catalog.md)
+ [Querying AWS service logs](querying-AWS-service-logs.md)
+ [Querying web server logs](querying-web-server-logs.md)

For considerations and limitations, see [Considerations and limitations for SQL queries in Amazon Athena](other-notable-limitations.md)\.