# When should I use Athena?<a name="when-should-i-use-ate"></a>

Athena helps you analyze unstructured, semi\-structured, and structured data stored in Amazon S3\. Examples include CSV, JSON, or columnar data formats such as Apache Parquet and Apache ORC\. You can use Athena to run ad\-hoc queries using ANSI SQL, without the need to aggregate or load the data into Athena\.

Athena integrates with Amazon QuickSight for easy data visualization\. You can use Athena to generate reports or to explore data with business intelligence tools or SQL clients connected with a JDBC or an ODBC driver\. For more information, see [What is Amazon QuickSight](https://docs.aws.amazon.com/quicksight/latest/user/welcome.html) in the *Amazon QuickSight User Guide* and [Connecting to Amazon Athena with ODBC and JDBC Drivers](athena-bi-tools-jdbc-odbc.md)\.

Athena integrates with the AWS Glue Data Catalog, which offers a persistent metadata store for your data in Amazon S3\. This allows you to create tables and query data in Athena based on a central metadata store available throughout your AWS account and integrated with the ETL and data discovery features of AWS Glue\. For more information, see [Integration with AWS Glue](glue-athena.md) and [What is AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) in the *AWS Glue Developer Guide*\.

For a list of AWS services that Athena leverages or integrates with, see [AWS Service Integrations with Athena](athena-aws-service-integrations.md)\.