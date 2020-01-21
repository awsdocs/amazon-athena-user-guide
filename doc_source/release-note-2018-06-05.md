# June 5, 2018<a name="release-note-2018-06-05"></a>

Published on *2018\-06\-05*

**Topics**
+ [Support for Views](#support-for-views)
+ [Improvements and Updates to Error Messages](#improvements)
+ [Bug Fixes](#bug-fixes)

## Support for Views<a name="support-for-views"></a>

Added support for views\. You can now use [CREATE VIEW](create-view.md), [DESCRIBE VIEW](describe-view.md), [DROP VIEW](drop-view.md), [SHOW CREATE VIEW](show-create-view.md), and [SHOW VIEWS](show-views.md) in Athena\. The query that defines the view runs each time you reference the view in your query\. For more information, see [Working with Views](views.md)\.

## Improvements and Updates to Error Messages<a name="improvements"></a>
+ Included a GSON 2\.8\.0 library into the CloudTrail SerDe, to solve an issue with the CloudTrail SerDe and enable parsing of JSON strings\.
+ Enhanced partition schema validation in Athena for Parquet, and, in some cases, for ORC, by allowing reordering of columns\. This enables Athena to better deal with changes in schema evolution over time, and with tables added by the AWS Glue Crawler\. For more information, see [Handling Schema Updates](handling-schema-updates-chapter.md)\.
+ Added parsing support for `SHOW VIEWS`\.
+ Made the following improvements to most common error messages: 
  + Replaced an Internal Error message with a descriptive error message when a SerDe fails to parse the column in an Athena query\. Previously, Athena issued an internal error in cases of parsing errors\. The new error message reads: "HIVE\_BAD\_DATA: Error parsing field value for field 0: java\.lang\.String cannot be cast to org\.openx\.data\.jsonserde\.json\.JSONObject"\.
  + Improved error messages about insufficient permissions by adding more detail\.

## Bug Fixes<a name="bug-fixes"></a>

Fixed the following bugs:
+ Fixed an issue that enables the internal translation of `REAL` to `FLOAT` data types\. This improves integration with the AWS Glue Crawler that returns `FLOAT` data types\. 
+ Fixed an issue where Athena was not converting AVRO `DECIMAL` \(a logical type\) to a `DECIMAL` type\. 
+ Fixed an issue where Athena did not return results for queries on Parquet data with `WHERE` clauses that referenced values in the `TIMESTAMP` data type\.