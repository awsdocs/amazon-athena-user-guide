# Understanding tables, databases, and the Data Catalog<a name="understanding-tables-databases-and-the-data-catalog"></a>

In Athena, tables and databases are containers for the metadata definitions that define a schema for underlying source data\. For each dataset, a table needs to exist in Athena\. The metadata in the table tells Athena where the data is located in Amazon S3, and specifies the structure of the data, for example, column names, data types, and the name of the table\. Databases are a logical grouping of tables, and also hold only metadata and schema information for a dataset\.

For each dataset that you'd like to query, Athena must have an underlying table it will use for obtaining and returning query results\. Therefore, before querying data, a table must be registered in Athena\. The registration occurs when you either create tables automatically or manually\.

Regardless of how the tables are created, the tables creation process registers the dataset with Athena\. This registration occurs in the AWS Glue Data Catalog and enables Athena to run queries on the data\.
+ To create a table automatically, use an AWS Glue crawler from within Athena\. For more information about AWS Glue and crawlers, see [Integration with AWS Glue](glue-athena.md)\. When AWS Glue creates a table, it registers it in its own AWS Glue Data Catalog\. Athena uses the AWS Glue Data Catalog to store and retrieve this metadata, using it when you run queries to analyze the underlying dataset\.

After you create a table, you can use [SQL SELECT](select.md) statements to query it, including getting [specific file locations for your source data](select.md#select-path)\. Your query results are stored in Amazon S3 in [the query result location that you specify](querying.md#query-results-specify-location)\.

The AWS Glue Data Catalog is accessible throughout your Amazon Web Services account\. Other AWS services can share the AWS Glue Data Catalog, so you can see databases and tables created throughout your organization using Athena and vice versa\. In addition, AWS Glue lets you automatically discover data schema and extract, transform, and load \(ETL\) data\.
+ To create a table manually:
  + Use the Athena console to run the **Create Table Wizard**\.
  + Use the Athena console to write Hive DDL statements in the Query Editor\.
  + Use the Athena API or CLI to run a SQL query string with DDL statements\.
  + Use the Athena JDBC or ODBC driver\.

When you create tables and databases manually, Athena uses HiveQL data definition language \(DDL\) statements such as `CREATE TABLE`, `CREATE DATABASE`, and `DROP TABLE` under the hood to create tables and databases in the AWS Glue Data Catalog\.

**Note**  
If you have tables in Athena created before August 14, 2017, they were created in an Athena\-managed internal data catalog that exists side\-by\-side with the AWS Glue Data Catalog until you choose to update\. For more information, see [Upgrading to the AWS Glue Data Catalog Step\-by\-Step](glue-upgrade.md)\.

When you query an existing table, under the hood, Amazon Athena uses Presto, a distributed SQL engine\. We have examples with sample data within Athena to show you how to create a table and then issue a query against it using Athena\. Athena also has a tutorial in the console that helps you get started creating a table based on data that is stored in Amazon S3\.
+ For a step\-by\-step tutorial on creating a table and writing queries in the Athena Query Editor, see [Getting Started](getting-started.md)\.
+ Run the Athena tutorial in the console\. This launches automatically if you log in to [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home) for the first time\. You can also choose **Tutorial** in the console to launch it\.