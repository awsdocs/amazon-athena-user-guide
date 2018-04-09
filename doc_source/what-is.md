# What is Amazon Athena?<a name="what-is"></a>

Amazon Athena is an interactive query service that makes it easy to analyze data directly in Amazon Simple Storage Service \(Amazon S3\) using standard SQL\. With a few actions in the AWS Management Console, you can point Athena at your data stored in Amazon S3 and begin using standard SQL to run ad\-hoc queries and get results in seconds\.

Athena is serverless, so there is no infrastructure to set up or manage, and you pay only for the queries you run\. Athena scales automatically—executing queries in parallel—so results are fast, even with large datasets and complex queries\.

## When should I use Athena?<a name="when-should-i-use-ate"></a>

Athena helps you analyze unstructured, semi\-structured, and structured data stored in Amazon S3\. Examples include CSV, JSON, or columnar data formats such as Apache Parquet and Apache ORC\. You can use Athena to run ad\-hoc queries using ANSI SQL, without the need to aggregate or load the data into Athena\.

Athena integrates with the AWS Glue Data Catalog, which offers a persistent metadata store for your data in Amazon S3\. This allows you to create tables and query data in Athena based on a central metadata store available throughout your AWS account and integrated with the ETL and data discovery features of AWS Glue\. For more information, see [Integration with AWS Glue](glue-athena.md) and [What is AWS Glue](http://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) in the *AWS Glue Developer Guide*\.

Athena integrates with Amazon QuickSight for easy data visualization\.

You can use Athena to generate reports or to explore data with business intelligence tools or SQL clients connected with a JDBC or an ODBC driver\. For more information, see [What is Amazon QuickSight](http://docs.aws.amazon.com/quicksight/latest/user/welcome.html) in the *Amazon QuickSight User Guide* and [Connecting to Amazon Athena with ODBC and JDBC Drivers](athena-bi-tools-jdbc-odbc.md)\.

You can create named queries with AWS CloudFormation and run them in Athena\. Named queries allow you to map a query name to a query and then call the query multiple times referencing it by its name\. For information, see [CreateNamedQuery](http://docs.aws.amazon.com/athena/latest/APIReference/API_CreateNamedQuery.html) in the *Amazon Athena API Reference*, and [AWS::Athena::NamedQuery](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-namedquery.html) in the *AWS CloudFormation User Guide*\.

## Accessing Athena<a name="accessing-ate"></a>

You can access Athena using the AWS Management Console, through a JDBC connection, using the Athena API, or using the Athena CLI\.
+ To get started with the console, see [Getting Started](getting-started.md)\.
+ To learn how to use JDBC, see [Connecting to Amazon Athena with JDBC](connect-with-jdbc.md)\.
+ To use the Athena API, see the [Amazon Athena API Reference](http://docs.aws.amazon.com/athena/latest/APIReference/Welcome.html)\.
+ To use the CLI, [install the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) and then type `aws athena help` from the command line to see available commands\. For information about available commands, see the [AWS Athena command line reference](http://docs.aws.amazon.com/cli/latest/reference/athena/)\.

## Understanding Tables, Databases, and the Data Catalog<a name="understanding-tables-databases-and-the-data-catalog"></a>

In Athena, tables and databases are containers for the metadata definitions that define a schema for underlying source data\. For each dataset, a table needs to exist in Athena\. The metadata in the table tells Athena where the data is located in Amazon S3, and specifies the structure of the data, for example, column names, data types, and the name of the table\. Databases are a logical grouping of tables, and also hold only metadata and schema information for a dataset\.

For each dataset that you'd like to query, Athena must have an underlying table it will use for obtaining and returning query results\. Therefore, before querying data, a table must be registered in Athena\. The registration occurs when you either create tables automatically or manually\.

Regardless of how the tables are created, the tables creation process registers the dataset with Athena\. This registration occurs either in the AWS Glue Data Catalog, or in the internal Athena data catalog and enables Athena to run queries on the data\.
+ To create a table automatically, use an AWS Glue crawler from within Athena\. For more information about AWS Glue and crawlers, see [Integration with AWS Glue](glue-athena.md)\. When AWS Glue creates a table, it registers it in its own AWS Glue Data Catalog\. Athena uses the AWS Glue Data Catalog to store and retrieve this metadata, using it when you run queries to analyze the underlying dataset\.

The AWS Glue Data Catalog is accessible throughout your AWS account\. Other AWS services can share the AWS Glue Data Catalog, so you can see databases and tables created throughout your organization using Athena and vice versa\. In addition, AWS Glue lets you automatically discover data schema and extract, transform, and load \(ETL\) data\.

**Note**  
You use the internal Athena data catalog in regions where AWS Glue is not available and where the AWS Glue Data Catalog cannot be used\.
+ To create a table manually:
  + Use the Athena console to run the **Create Table Wizard**\.
  + Use the Athena console to write Hive DDL statements in the Query Editor\.
  + Use the Athena API or CLI to execute a SQL query string with DDL statements\.
  + Use the Athena JDBC or ODBC driver\.

When you create tables and databases manually, Athena uses HiveQL data definition language \(DDL\) statements such as `CREATE TABLE`, `CREATE DATABASE`, and `DROP TABLE` under the hood to create tables and databases in the AWS Glue Data Catalog, or in its internal data catalog in those regions where AWS Glue is not available\.

**Note**  
If you have tables in Athena created before August 14, 2017, they were created in an Athena\-managed data catalog that exists side\-by\-side with the AWS Glue Data Catalog until you choose to update\. For more information, see [Upgrading to the AWS Glue Data Catalog Step\-by\-Step](glue-upgrade.md)\.

When you query an existing table, under the hood, Amazon Athena uses Presto, a distributed SQL engine\. We have examples with sample data within Athena to show you how to create a table and then issue a query against it using Athena\. Athena also has a tutorial in the console that helps you get started creating a table based on data that is stored in Amazon S3\.
+ For a step\-by\-step tutorial on creating a table and write queries in the Athena Query Editor, see [Getting Started](getting-started.md)\.
+ Run the Athena tutorial in the console\. This launches automatically if you log in to [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home) for the first time\. You can also choose **Tutorial** in the console to launch it\.