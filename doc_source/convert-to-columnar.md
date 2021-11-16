# Converting to Columnar Formats<a name="convert-to-columnar"></a>

Your Amazon Athena query performance improves if you convert your data into open source columnar formats, such as [Apache Parquet](https://parquet.apache.org) or [ORC](https://orc.apache.org/)\.

Options for easily converting source data such as JSON or CSV into a columnar format include using [CREATE TABLE AS](ctas.md) queries or running jobs in AWS Glue\.
+ You can use `CREATE TABLE AS` \(CTAS\) queries to convert data into Parquet or ORC in one step\. For an example, see [Example: Writing Query Results to a Different Format](https://docs.aws.amazon.com/athena/latest/ug/ctas-examples.html#ctas-example-format) on the [Examples of CTAS Queries](ctas-examples.md) page\.
+ For information about running an AWS Glue job to transform CSV data to Parquet, see the section "Transform the data from CSV to Parquet format" in the AWS Big Data blog post [Build a Data Lake Foundation with AWS Glue and Amazon S3](http://aws.amazon.com/blogs/big-data/build-a-data-lake-foundation-with-aws-glue-and-amazon-s3/)\. AWS Glue supports using the same technique to convert CSV data to ORC, or JSON data to either Parquet or ORC\.