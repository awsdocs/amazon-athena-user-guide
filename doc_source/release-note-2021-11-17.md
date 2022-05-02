# November 17, 2021<a name="release-note-2021-11-17"></a>

Published on 2021\-11\-17

[Amazon Athena](http://aws.amazon.com/athena/) now supports partition indexing to accelerate queries on partitioned tables in the [AWS Glue Data Catalog](http://aws.amazon.com/glue/)\.

When querying partitioned tables, Athena retrieves and filters the available table partitions to the subset relevant to your query\. As new data and partitions are added, more time is required to process the partitions and query runtime can increase\. To optimize partition processing and improve query performance on highly partitioned tables, Athena now supports [AWS Glue partition indexes](https://docs.aws.amazon.com/glue/latest/dg/partition-indexes.html)\.

For more information, see [AWS Glue partition indexing and filtering](glue-best-practices.md#glue-best-practices-partition-index)\.