# July 16, 2021<a name="release-note-2021-07-16"></a>

Published on 2021\-07\-16

Amazon Athena has updated its integration with Apache Hudi\. Hudi is an open\-source data management framework used to simplify incremental data processing in Amazon S3 data lakes\. The updated integration enables you to use Athena to query Hudi 0\.8\.0 tables managed through Amazon EMR, Apache Spark, Apache Hive or other compatible services\. In addition, Athena now supports two additional features: snapshot queries on Merge\-on\-Read \(MoR\) tables and read support on bootstrapped tables\.

Apache Hudi provides record\-level data processing that can help you simplify development of Change Data Capture \(CDC\) pipelines, comply with GDPR\-driven updates and deletes, and better manage streaming data from sensors or devices that require data insertion and event updates\. The 0\.8\.0 release makes it easier to migrate large Parquet tables to Hudi without copying data so you can query and analyze them through Athena\. You can use Athena's new support for snapshot queries to have near real\-time views of your streaming table updates\.

To learn more about using Hudi with Athena, see [Using Athena to Query Apache Hudi Datasets](querying-hudi.md)\.