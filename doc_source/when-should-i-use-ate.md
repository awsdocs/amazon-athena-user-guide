# When should I use Athena?<a name="when-should-i-use-ate"></a>

Query services like Amazon Athena, data warehouses like Amazon Redshift, and sophisticated data processing frameworks like Amazon EMR all address different needs and use cases\. The following guidance can help you choose one or more services based on your requirements\.

## Amazon Athena<a name="when-should-i-use-athena"></a>

Athena helps you analyze unstructured, semi\-structured, and structured data stored in Amazon S3\. Examples include CSV, JSON, or columnar data formats such as Apache Parquet and Apache ORC\. You can use Athena to run ad\-hoc queries using ANSI SQL, without the need to aggregate or load the data into Athena\.

Athena integrates with Amazon QuickSight for easy data visualization\. You can use Athena to generate reports or to explore data with business intelligence tools or SQL clients connected with a JDBC or an ODBC driver\. For more information, see [What is Amazon QuickSight](https://docs.aws.amazon.com/quicksight/latest/user/welcome.html) in the *Amazon QuickSight User Guide* and [Connecting to Amazon Athena with ODBC and JDBC drivers](athena-bi-tools-jdbc-odbc.md)\.

Athena integrates with the AWS Glue Data Catalog, which offers a persistent metadata store for your data in Amazon S3\. This allows you to create tables and query data in Athena based on a central metadata store available throughout your Amazon Web Services account and integrated with the ETL and data discovery features of AWS Glue\. For more information, see [Integration with AWS Glue](glue-athena.md) and [What is AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) in the *AWS Glue Developer Guide*\.

Amazon Athena makes it easy to run interactive queries against data directly in Amazon S3 without having to format data or manage infrastructure\. For example, Athena is useful if you want to run a quick query on web logs to troubleshoot a performance issue on your site\. With Athena, you can get started fast: you just define a table for your data and start querying using standard SQL\.

You should use Amazon Athena if you want to run interactive ad hoc SQL queries against data on Amazon S3, without having to manage any infrastructure or clusters\. Amazon Athena provides the easiest way to run ad hoc queries for data in Amazon S3 without the need to setup or manage any servers\.

For a list of AWS services that Athena leverages or integrates with, see [AWS service integrations with Athena](athena-aws-service-integrations.md)\. 

## Amazon EMR<a name="when-should-i-use-emr"></a>

Amazon EMR makes it simple and cost effective to run highly distributed processing frameworks such as Hadoop, Spark, and Presto when compared to on\-premises deployments\. Amazon EMR is flexible – you can run custom applications and code, and define specific compute, memory, storage, and application parameters to optimize your analytic requirements\.

In addition to running SQL queries, Amazon EMR can run a wide variety of scale\-out data processing tasks for applications such as machine learning, graph analytics, data transformation, streaming data, and virtually anything you can code\. You should use Amazon EMR if you use custom code to process and analyze extremely large datasets with the latest big data processing frameworks such as Spark, Hadoop, Presto, or Hbase\. Amazon EMR gives you full control over the configuration of your clusters and the software installed on them\.

You can use Amazon Athena to query data that you process using Amazon EMR\. Amazon Athena supports many of the same data formats as Amazon EMR\. Athena's data catalog is Hive metastore compatible\. If you use EMR and already have a Hive metastore, you can run your DDL statements on Amazon Athena and query your data immediately without affecting your Amazon EMR jobs\.

## Amazon Redshift<a name="when-should-i-use-redshift"></a>

A data warehouse like Amazon Redshift is your best choice when you need to pull together data from many different sources – like inventory systems, financial systems, and retail sales systems – into a common format, and store it for long periods of time\. If you want to build sophisticated business reports from historical data, then a data warehouse like Amazon Redshift is the best choice\. The query engine in Amazon Redshift has been optimized to perform especially well on running complex queries that join large numbers of very large database tables\. When you need to run queries against highly structured data with lots of joins across lots of very large tables, choose Amazon Redshift\.

For more information about when to use Athena, consult the following resources:
+ [When to use Athena vs other big data services](http://aws.amazon.com/athena/faqs/#When_to_use_Athena_vs_other_big_data_services) 
+ [Amazon Athena overview](http://aws.amazon.com/athena/) 
+ [Amazon Athena features](http://aws.amazon.com/athena/features/) 
+ [Amazon Athena FAQs](http://aws.amazon.com/athena/faqs/)
+ [Amazon Athena blog posts](http://aws.amazon.com/athena/resources/#Blog_posts) 