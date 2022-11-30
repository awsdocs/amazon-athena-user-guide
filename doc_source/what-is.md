# What is Amazon Athena?<a name="what-is"></a>

Amazon Athena is an interactive query service that makes it easy to analyze data directly in Amazon Simple Storage Service \(Amazon S3\) using standard [SQL](ddl-sql-reference.md)\. With a few actions in the AWS Management Console, you can point Athena at your data stored in Amazon S3 and begin using standard SQL to run ad\-hoc queries and get results in seconds\.

For more information, see [Getting started](getting-started.md)\.

Amazon Athena also makes it easy to interactively run data analytics using Apache Spark without having to plan for, configure, or manage resources\. When you run Apache Spark applications on Athena, you submit Spark code for processing and receive the results directly\. Use the simplified notebook experience in Amazon Athena console to develop Apache Spark applications using Python or [ Athena notebook APIs](notebooks-spark-api-list.md)\.

For more information, see [Getting started with Apache Spark on Amazon Athena](notebooks-spark-getting-started.md)\.

Athena SQL and Apache Spark on Amazon Athena are serverless, so there is no infrastructure to set up or manage, and you pay only for the queries you run\. Athena scales automatically—running queries in parallel—so results are fast, even with large datasets and complex queries\.

**Topics**
+ [When should I use Athena?](when-should-i-use-ate.md)
+ [AWS service integrations with Athena](athena-aws-service-integrations.md)
+ [Setting up](setting-up.md)
+ [Accessing Athena](accessing-athena.md)