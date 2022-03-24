# February 23, 2022<a name="release-note-2022-02-23"></a>

Published on 2022\-02\-23

Athena announces the following fixes and performance improvements\.
+ Memory handling improvements to enhance performance and reduce memory errors\.
+ Athena now reads ORC timestamp columns with time zone information stored in stripe footers and writes ORC files with time zone \(UTC\) in footers\. This only impacts the behavior of ORC timestamp reads if the ORC file to be read was created in a non\-UTC time zone environment\.
+ Fixed incorrect symlink table size estimates that resulted in suboptimal query plans\.
+ Lateral exploded views can now be queried in the Athena console from Hive metastore data sources\.
+ Improved Amazon S3 read error messages to include more detailed [Amazon S3 error code](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html#ErrorCodeList) information\.
+ Fixed an issue that caused output files in ORC format to become incompatible with Apache Hive 3\.1\.
+ Fixed an issue that caused table names with quotes to fail in certain DML and DDL queries\.