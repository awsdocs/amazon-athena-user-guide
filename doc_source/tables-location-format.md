# Table Location in Amazon S3<a name="tables-location-format"></a>

When you run a `CREATE TABLE` query in Athena, you register your table with the AWS Glue Data Catalog\. If you are using Athena own catalog, we highly recommend that you [upgrade](glue-upgrade.md) to the AWS Glue Data Catalog\. You specify the path to your data in the `LOCATION` property, as shown in the following abbreviated example:

```
CREATE EXTERNAL TABLE `test_table`(
...
)
ROW FORMAT ...
STORED AS INPUTFORMAT ...
OUTPUTFORMAT ...
LOCATION s3://bucketname/prefix/
```

This location in Amazon S3 comprises *all* of the files representing your table\. For more information, see [Using Folders](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html) in the *Amazon Simple Storage Service Console User Guide\.* 

**Important**  
Athena reads *all* data stored under the `'s3://bucketname/prefix/'`\. If you have data that you do *not* want Athena to read, do not store that data in the same Amazon S3 prefix as the data you want Athena to read\. If you are leveraging partitioning, to ensure Athena scans data within a partition, your `WHERE` filter must include the partition\. For more information, see [Table Location and Partitions](#table-location-and-partitions)\.

Use these tips and examples when you specify the Amazon S3 location of your data in the Athena `CREATE TABLE` statement:
+ In the `LOCATION` clause, use a trailing slash\.

   **Use**:

  ```
  s3://bucketname/prefix/
  ```
+ Do not use any of the following items for specifying the location for your data\.
  + Do not use filenames, underscores, wildcards, or glob patterns for specifying file locations\.
  + Do not add the full HTTP notation, such as `s3.amazon.com` to the Amazon S3 bucket path\.
  + Do not use empty prefixes \(with the extra /\) in the path, as follows: `S3://bucketname/prefix//prefix/`\. While this is a valid Amazon S3 path, Athena does not allow it and changes it to `s3://bucketname/prefix/prefix/`, removing the extra /\. 

     **Do not use**:

    ```
    s3://path_to_bucket
    s3://path_to_bucket/*
    s3://path_to_bucket/mySpecialFile.dat
    s3://bucketname/prefix/filename.csv
    s3://test-bucket.s3.amazon.com
    S3://bucket/prefix//prefix/
    arn:aws:s3:::bucketname/prefix
    ```

## Table Location and Partitions<a name="table-location-and-partitions"></a>

 Your source data may be grouped into Amazon S3 prefixes, also known as partitions, based on a set of columns\. For example, these columns may represent the year, month, and day the particular record was created\. 

When creating a table, you can choose to make it partitioned\. When Athena executes an SQL query against a non\-partitioned table, it uses the `LOCATION` property from the table definition as the base path to list and then scan all available files\. Before a partitioned table can be queried, you must first update the AWS Glue Data Catalog with partition information\. This information represents the schema of files within the particular partition and the `LOCATION` of files in Amazon S3 for the partition\. To learn how the AWS Glue crawler adds partitions, see [How Does a Crawler Determine When to Create Partitions? ](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html#crawler-s3-folder-table-partition) in the *AWS Glue Developer Guide*\. To learn how to configure the crawler so that it creates tables for data in existing partitions, see [Using Multiple Data Sources with Crawlers](glue-best-practices.md#schema-crawlers-data-sources)\. You can also create partitions in a table directly in Athena\. For more information, see [Partitioning Data](partitions.md)\.

When Athena executes a query on a partitioned table, it first checks to see if any partitioned columns were used in the `WHERE` clause of the query\. If partitioned columns were used, Athena requests the AWS Glue Data Catalog to return the partition specification matching the specified partition columns\. The partition specification includes the `LOCATION` property which tells Athena which Amazon S3 prefix it needs to use when reading data\. In this case, *only* data stored in this prefix is scanned\. If you do not use partitioned columns in the `WHERE` clause, Athena scans all of the files starting at the base Amazon S3 path specified by the `LOCATION` property in the main table definition\. 

For examples of using partitioning with Athena to improve query performance and reduce query costs, see [Top Performance Tuning Tips for Amazon Athena](http://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)\.