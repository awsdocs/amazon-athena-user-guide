# April 22, 2022<a name="release-note-2022-04-22"></a>

Published on 2022\-04\-22

Athena announces the following fixes and improvements\.
+ Fixed an issue in the [partition indices and filtering feature](http://aws.amazon.com/blogs/big-data/improve-amazon-athena-query-performance-using-aws-glue-data-catalog-partition-indexes/) with the partition cache that occurred when the following conditions were met:
  + The `partition_filtering.enabled` key was set to `true` in the AWS Glue table properties for a table\.
  + The same table was used multiple times with different partition filter values\.