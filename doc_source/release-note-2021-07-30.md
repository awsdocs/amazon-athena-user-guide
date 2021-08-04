# July 30, 2021<a name="release-note-2021-07-30"></a>

Published on 2021\-07\-30

Athena announces the following feature enhancements and bug fixes\.
+ **Dynamic filtering and partition pruning** – Improvements increase performance and reduce the amount of data scanned in certain queries, as in the following example\. 

  This example assumes that `Table_B` is an unpartitioned table that has file sizes that add up to less than 20Mb\. For queries like this, less data is read from `Table_A` and the query completes more quickly\.

  ```
  SELECT *
  FROM Table_A
  JOIN Table_B ON Table_A.date = Table_B.date
  WHERE Table_B.column_A = "value"
  ```
+ **ORDER BY with LIMIT, DISTINCT with LIMIT** – Performance improvements to queries that use `ORDER BY` or `DISTINCT` followed by a `LIMIT` clause\.
+ **S3 Glacier Deep Archive files** – When Athena queries a table that contains a mix of [S3 Glacier Deep Archive files](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html#sc-glacier) and non\-S3 Glacier files, Athena now skips the S3 Glacier Deep Archive files for you\. Previously, you were required to manually move these files from the query location or the query would fail\. If you want to use Athena to query objects in S3 Glacier Deep Archive storage, you must restore them\. For more information, see [Restoring an archived object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects.html) in the *Amazon S3 User Guide*\.
+ Fixed a bug in which empty files created by the CTAS `bucketed_by` [table property](create-table-as.md#ctas-table-properties) were not encrypted correctly\.