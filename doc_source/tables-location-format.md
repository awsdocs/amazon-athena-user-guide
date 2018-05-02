# Table Location in Amazon S3<a name="tables-location-format"></a>

When you run a `CREATE TABLE AS` query in Athena, you register your table with the data catalog that Athena uses\. If you migrated to AWS Glue, this is the catalog from AWS Glue\. You also specify the location in Amazon S3 for your table in this format: `s3://bucketname/keyname`\.

Use these tips and examples when you specify the location in Amazon S3\.
+ Athena reads all files in an Amazon S3 location you specify in the `CREATE TABLE` statement, and cannot ignore any files included in the prefix\. When you create tables, include in the Amazon S3 path only the files you want Athena to read\. Use AWS Lambda functions to scan files in the source location, remove any empty files, and move unneeded files to another location\.
+ In the `LOCATION` clause, use a trailing slash for your folder or bucket\.

 **Use**:

```
s3://bucketname/prefix/
```

Do not use any of the following items in file locations\.
+ Do not use filenames, underscores, wildcards, or glob patterns for specifying file locations\.
+ Do not add the full HTTP notation, such as `s3.amazon.com` to the Amazon S3 bucket path\.

 **Do not use**:

```
s3://path_to_bucket
s3://path_to_bucket/*
s3://path_to_bucket/mySpecialFile.dat
s3://bucketname/prefix/filename.csv
s3://test-bucket.s3.amazon.com
arn:aws:s3:::bucketname/prefix
```