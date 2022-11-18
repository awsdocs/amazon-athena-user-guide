# Using Iceberg tables<a name="querying-iceberg"></a>

Athena supports read, time travel, write, and DDL queries for Apache Iceberg tables that use the Apache Parquet format for data and the AWS Glue catalog for their metastore\.

[Apache Iceberg](https://iceberg.apache.org/) is an open table format for very large analytic datasets\. Iceberg manages large collections of files as tables, and it supports modern analytical data lake operations such as record\-level insert, update, delete, and time travel queries\. The Iceberg specification allows seamless table evolution such as schema and partition evolution, and its design is optimized for usage on Amazon S3\. Iceberg also helps guarantee data correctness under concurrent write scenarios\.

For more information about Apache Iceberg, see [https://iceberg\.apache\.org/](https://iceberg.apache.org/)\.

## Considerations and limitations<a name="querying-iceberg-considerations-and-limitations"></a>

Athena support for Iceberg tables has the following limitations:
+ **Tables with AWS Glue catalog only** – Only Iceberg tables created against the AWS Glue catalog based on specifications defined by the [open source glue catalog implementation](https://iceberg.apache.org/docs/latest/aws/#glue-catalog) are supported from Athena\.
+ **Table locking support by AWS Glue only** – Unlike the open source Glue catalog implementation, which supports plug\-in custom locking, Athena supports AWS Glue optimistic locking only\. Using Athena to modify an Iceberg table with any other lock implementation will cause potential data loss and break transactions\.
+ **Supported file formats** – Iceberg file format support in Athena depends on the Athena engine version, as shown in the following table\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/querying-iceberg.html)
+ **Iceberg v2 tables** – Athena only creates and operates on Iceberg v2 tables\. For the difference between v1 and v2 tables, see [Format version changes](https://iceberg.apache.org/spec/#appendix-e-format-version-changes) in the Apache Iceberg documentation\.
+ **Display of time types without time zone** – The time and timestamp without time zone types are displayed in UTC\. If the time zone is unspecified in a filter expression on a time column, UTC is used\.
+ **Timestamp related data precision** – While Iceberg supports microsecond precision for the timestamp data type, Athena supports only millisecond precision for timestamps in both reads and writes\. Athena only retains millisecond precision in time related columns for data that is rewritten during manual compaction operations\.
+ **Unsupported operations** – The following Athena operations are not supported for Iceberg tables\. 
  + [ALTER TABLE SET LOCATION](alter-table-set-location.md)
+ **Views** – Use `CREATE VIEW` to create Athena views as described in [Working with views](views.md)\. If you are interested in using the [Iceberg view specification](https://github.com/apache/iceberg/blob/master/format/view-spec.md) to create views, contact [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\. 

If you would like Athena to support a particular feature, send feedback to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\.

**Topics**
+ [Considerations and limitations](#querying-iceberg-considerations-and-limitations)
+ [Creating Iceberg tables](querying-iceberg-creating-tables.md)
+ [Managing Iceberg tables](querying-iceberg-managing-tables.md)
+ [Evolving Iceberg table schema](querying-iceberg-evolving-table-schema.md)
+ [Querying Iceberg table data and performing time travel](querying-iceberg-table-data.md)
+ [Updating Iceberg table data](querying-iceberg-updating-iceberg-table-data.md)
+ [Optimizing Iceberg tables](querying-iceberg-data-optimization.md)
+ [Supported data types](querying-iceberg-supported-data-types.md)
+ [Other Athena operations on Iceberg tables](querying-iceberg-additional-operations.md)