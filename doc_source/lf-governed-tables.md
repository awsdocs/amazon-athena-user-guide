# Using governed tables<a name="lf-governed-tables"></a>

To help ensure secure and reliable transactions, Athena supports read operations using [AWS Lake Formation governed tables](https://docs.aws.amazon.com/lake-formation/latest/dg/governed-tables-main.html)\. The security features help ensure that your users have access only to the data in your Amazon S3 based data lakes to which they're authorized\. The ACID features help ensure that queries are reliable in the face of complex changes to the underlying data\.

Use Lake Formation data filtering to secure the tables in your Amazon S3 data lake by granting permissions at the column, row, and cell levels\. These permissions are enforced when Athena users query your data\. This fine level of control means that you can grant users access to sensitive information without using coarse\-grained masking that would otherwise impede their analyses\.

Governed tables in AWS Lake Formation provide the following capabilities:
+ **ACID transactions** – Read and write to and from multiple tables in your Amazon S3 data lake using ACID \(atomic, consistent, isolated, and durable\) transactions\. You can commit or cancel a transaction, or Lake Formation cancels the transaction if it finds an error\. In Athena, governed table queries are read\-only\.
+ **Time travel and version travel queries** – Each governed table maintains a versioned manifest of the Amazon S3 objects that it comprises\. Previous versions of the manifest can be used for time travel and version travel queries\. You can perform time travel queries in Athena to query Amazon S3 for historical data as of a specified date and time\. You can perform version travel queries in Athena to query Amazon S3 for historical data as of a specified version snapshot ID\.
+ **Enforce row\-level permissions** – You can grant `SELECT` on a table and provide a predicate such as `region=us-west-1` and `year=2019`\. Only table rows for which the predicate is true are returned\. Row\-level permissions are also available in Lake Formation for non\-governed tables\. For more information, see [Data filtering and cell\-level security in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/data-filtering.html) in the *AWS Lake Formation Developer Guide*\.
+ **Enforce cell\-level permissions** – Cell\-level security is a special case of row\-level security that adds the ability to specify columns and rows in the same policy\.
+ **Enforce column\-level permissions** – You can use AWS Lake Formation column\-level permissions to restrict access to specific columns in a table\. Column\-level permissions are also available in Lake Formation for non\-governed tables\. For more information, see [Data filtering and cell\-level security in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/data-filtering.html) in the *AWS Lake Formation Developer Guide*\.
+ **Catalog transactions** – Athena supports creation, deletion, and schema updates of governed tables with transaction semantics\. You can use this schema evolution support to query across schema or partition changes when the schema changes are forward compatible\. To query earlier schemas that are not forward compatible, use time travel queries\. 
+ **Automatic data compaction** – For improved performance, Lake Formation automatically compacts small Amazon S3 objects from governed tables into larger objects\.
+ **Predicate pushdown** – Predicate parts of SQL queries can be "pushed" closer to where the data lives\. This optimization can drastically reduce query processing time by filtering out data earlier rather than later\. 

For more information about governed tables, see [Managing governed tables](https://docs.aws.amazon.com/lake-formation/latest/dg/governed-tables-main.html) in the *AWS Lake Formation Developer Guide*\.

## Considerations and limitations<a name="lf-governed-tables-considerations-and-limitations"></a>

Support for Lake Formation governed tables in Athena has the following limitations:
+ **Read and create governed table operations only** – Only read and create governed table operations are supported\. Delete, insert into, and update operations on Lake Formation governed tables are not supported from Athena\.
+  **Automatic compaction** – The Lake Formation automatic compaction feature is not manageable from Athena\. 
+ **GetQueryStatistics permission required** – Athena users must add the `lakeformation:GetQueryStatistics` permission in IAM\.
+ **Error when querying certain nested columns on an empty dataset** – Currently, querying columns of the form `Array<Struct<Array<data_type>>>` on an empty dataset produces the error GENERIC\_INTERNAL\_ERROR: Exception while processing column\.
+ **Views** – You cannot use views with governed tables\.

## Getting started<a name="lf-governed-tables-getting-started"></a>

To get started using Lake Formation governed tables in Athena, perform the following step:
+ **Create a governed table** – To create a governed table from the Athena console, specify the table property `'table_type'='LAKEFORMATION_GOVERNED'` in your `CREATE TABLE` statement\. You can also use the AWS Lake Formation console, AWS Glue API, or AWS Command Line Interface \(AWS CLI\) to create one or more governed tables\.

  For more information, see [Creating governed tables](https://docs.aws.amazon.com/lake-formation/latest/dg/create-gov-table.html) in the *AWS Lake Formation Developer Guide*\. To register the Amazon S3 objects before you query them, call the Lake Formation `UpdateTableObjects` operation within a transaction\.