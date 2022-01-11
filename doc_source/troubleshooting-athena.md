# Troubleshooting in Athena<a name="troubleshooting-athena"></a>

The Athena team has gathered the following troubleshooting information from customer issues\. Although not comprehensive, it includes advice regarding some common performance, timeout, and out of memory issues\.

**Topics**
+ [CREATE TABLE AS SELECT \(CTAS\)](#troubleshooting-athena-create-table-as-select-ctas)
+ [Data File Issues](#troubleshooting-athena-data-file-issues)
+ [Federated Queries](#troubleshooting-athena-federated-queries)
+ [JSON Related Errors](#troubleshooting-athena-json-related-errors)
+ [MSCK REPAIR TABLE](#troubleshooting-athena-msck-repair-table)
+ [Output Issues](#troubleshooting-athena-output-issues)
+ [Partitioning Issues](#troubleshooting-athena-partitioning-issues)
+ [Permissions](#troubleshooting-athena-permissions)
+ [Query Syntax Issues](#troubleshooting-athena-query-syntax-issues)
+ [Throttling Issues](#troubleshooting-athena-throttling-issues)
+ [Views](#troubleshooting-athena-views)
+ [Workgroups](#troubleshooting-athena-workgroups)
+ [Additional Resources](#troubleshooting-athena-additional-resources)

## CREATE TABLE AS SELECT \(CTAS\)<a name="troubleshooting-athena-create-table-as-select-ctas"></a>

### Duplicated data occurs with concurrent CTAS statements<a name="troubleshooting-athena-duplicated-data-occurs-with-concurrent-ctas-statements"></a>

Athena does not maintain concurrent validation for CTAS\. Make sure that there is no duplicate CTAS statement for the same location at the same time\. Even if a CTAS or INSERT INTO statement fails, orphaned data can be left in the data location specified in the statement\.

## Data File Issues<a name="troubleshooting-athena-data-file-issues"></a>

### Athena cannot read hidden files<a name="troubleshooting-athena-athena-cannot-read-hidden-files"></a>

Athena treats sources files that start with an underscore \(\_\) or a dot \(\.\) as hidden\. To work around this limitation, rename the files\.

### Athena cannot read files stored in Amazon S3 Glacier<a name="troubleshooting-athena-athena-cannot-read-files-stored-in-the-glacier-storage-class"></a>

Athena does not support querying the data in the [S3 Glacier Flexible Retrieval](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html#sc-glacier) or S3 Glacier Deep Archive storage classes\. Objects in the S3 Glacier and S3 Glacier Deep Archive storage classes are ignored\. Data that is moved or transitioned to one of these classes are no longer readable or queryable by Athena even after storage class objects are restored\. To make the restored objects that you want to query readable by Athena, copy the restored objects back into Amazon S3 to change their storage class, or use the Amazon S3 Glacier Instant Retrieval storage class instead, which is queryable by Athena\. For more information, see [Amazon S3 Glacier Instant Retrieval storage class](http://aws.amazon.com/s3/storage-classes/glacier/instant-retrieval/)\.

### Athena reads files that I excluded from the AWS Glue crawler<a name="troubleshooting-athena-athena-reads-files-that-i-excluded-from-the-glue-crawler"></a>

Athena does not recognize [exclude patterns](https://docs.aws.amazon.com/glue/latest/dg/define-crawler.html#crawler-data-stores-exclude) that you specify an AWS Glue crawler\. For example, if you have an Amazon S3 bucket that contains both `.csv` and `.json` files and you exclude the `.json` files from the crawler, Athena queries both groups of files\. To avoid this, place the files that you want to exclude in a different location\.

### HIVE\_BAD\_DATA: Error parsing field value<a name="troubleshooting-athena-hive_bad_data-error-parsing-field-value"></a>

This error can occur in the following scenarios:
+ The data type defined in the table doesn't match the source data, or a single field contains different types of data\. For suggested resolutions, see [My Amazon Athena query fails with the error "HIVE\_BAD\_DATA: Error parsing field value for field X: For input string: "12312845691""](http://aws.amazon.com/premiumsupport/knowledge-center/athena-hive-bad-data-parsing-field-value/) in the AWS Knowledge Center\.
+ Null values are present in an integer field\. One workaround is to create the column with the null values as `string` and then use `CAST` to convert the field in a query, supplying a default value of `0` for nulls\. For more information, see [When I query CSV data in Athena, I get the error "HIVE\_BAD\_DATA: Error parsing field value '' for field X: For input string: """](http://aws.amazon.com/premiumsupport/knowledge-center/athena-hive-bad-data-error-csv/) in the AWS Knowledge Center\.

### HIVE\_CANNOT\_OPEN\_SPLIT: Error opening Hive split s3://*bucket\-name*<a name="troubleshooting-athena-hive_cannot_open_split-error-opening-hive-split-s3bucket-name"></a>

This error can occur when you query an Amazon S3 bucket prefix that has a large number of objects\. For more information, see [How do I resolve the "HIVE\_CANNOT\_OPEN\_SPLIT: Error opening Hive split s3://awsdoc\-example\-bucket/: Slow Down" error in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/hive-cannot-open-split-503-athena/) in the AWS Knowledge Center\.

### HIVE\_CURSOR\_ERROR: com\.amazonaws\.services\.s3\.model\.AmazonS3Exception: The specified key does not exist<a name="troubleshooting-athena-hive_cursor_error-com.amazonaws.services.s3.model.amazons3exception-the-specified-key-does-not-exist"></a>

This error usually occurs when a file is removed when a query is running\. Either rerun the query, or check your workflow to see if another job or process is modifying the files when the query is running\.

### HIVE\_CURSOR\_ERROR: Unexpected end of input stream<a name="troubleshooting-athena-hive_cursor_error-unexpected-end-of-input-stream"></a>

This message indicates the file is either corrupted or empty\. Check the integrity of the file and rerun the query\.

### HIVE\_FILESYSTEM\_ERROR: Incorrect fileSize *1234567* for file<a name="troubleshooting-athena-hive_filesystem_error-incorrect-file-size"></a>

This message can occur when a file has changed between query planning and query execution\. It usually occurs when a file on Amazon S3 is replaced in\-place \(for example, a `PUT` is performed on a key where an object already exists\)\. Athena does not support deleting or replacing the contents of a file when a query is running\. To avoid this error, schedule jobs that overwrite or delete files at times when queries do not run, or only write data to new files or partitions\.

### HIVE\_UNKNOWN\_ERROR: Unable to create input format<a name="troubleshooting-athena-hive_unknown_error-unable-to-create-input-format"></a>

This error can be a result of issues like the following:
+ The AWS Glue crawler wasn't able to classify the data format
+ Certain AWS Glue table definition properties are empty
+ Athena doesn't support the data format of the files in Amazon S3

For more information, see [How do I resolve the error "unable to create input format" in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-unable-to-create-input-format/) in the AWS Knowledge Center or watch the Knowledge Center [video](https://www.youtube.com/watch?v=CGzXW3hRa8g)\.

### org\.apache\.parquet\.io\.GroupColumnIO cannot be cast to org\.apache\.parquet\.io\.PrimitiveColumnIO<a name="troubleshooting-athena-org.apache.parquet.io.groupcolumnio-cannot-be-cast-to-org.apache.parquet.io.primitivecolumnio"></a>

This error is caused by a parquet schema mismatch\. A column that has a non\-primitive type \(for example, `array`\) has been declared as a primitive type \(for example, `string`\) in AWS Glue\. To troubleshoot this issue, check the data schema in the files and compare it with schema declared in AWS Glue\.

### The S3 location provided to save your query results is invalid\.<a name="troubleshooting-athena-the-s3-location-provided-to-save-your-query-results-is-invalid."></a>

Make sure that you have specified a valid S3 location for your query results\. For more information, see [Specifying a Query Result Location](querying.md#query-results-specify-location) in the [Working with Query Results, Recent Queries, and Output Files](querying.md) topic\.

## Federated Queries<a name="troubleshooting-athena-federated-queries"></a>

For information on troubleshooting federated queries, see [Common\_Problems](https://github.com/awslabs/aws-athena-query-federation/wiki/Common_Problems) in the awslabs/aws\-athena\-query\-federation section of GitHub\.

## JSON Related Errors<a name="troubleshooting-athena-json-related-errors"></a>

### NULL or incorrect data errors when trying to read JSON data<a name="troubleshooting-athena-null-or-incorrect-data-errors-when-trying-to-read-json-data"></a>

NULL or incorrect data errors when you try read JSON data can be due to a number of causes\. To identify lines that are causing errors when you are using the OpenX SerDe, set `ignore.malformed.json` to `true`\. Malformed records will return as NULL\. For more information, see [I get errors when I try to read JSON data in Amazon Athena](http://aws.amazon.com/premiumsupport/knowledge-center/error-json-athena/) in the AWS Knowledge Center or watch the Knowledge Center [video](https://youtu.be/ME7Pv1qPFLM)\.

### HIVE\_BAD\_DATA: Error parsing field value for field 0: java\.lang\.String cannot be cast to org\.openx\.data\.jsonserde\.json\.JSONObject<a name="troubleshooting-athena-hive-bad-data-openx-json-serde"></a>

The [OpenX JSON SerDe](json-serde.md#openx-json-serde) throws this error when it fails to parse a column in an Athena query\. This can happen if you define a column as a `map` or `struct`, but the underlying data is actually a `string`, `int`, or other primitive type\.

### HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object \- JSONException: Duplicate key<a name="troubleshooting-athena-hive_cursor_error-row-is-not-a-valid-json-object---jsonexception-duplicate-key"></a>

This error occurs when you use Athena to query AWS Config resources that have multiple tags with the same name in different case\. The solution is to run *CREATE TABLE* using *WITH SERDEPROPERTIES 'case\.insensitive'='false'* and map the names\. For information about `case.insensitive` and mapping, see [JSON SerDe Libraries](json-serde.md)\. For more information, see [How do I resolve "HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object \- JSONException: Duplicate key" when reading files from AWS Config in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/json-duplicate-key-error-athena-config/) in the AWS Knowledge Center\.

### HIVE\_CURSOR\_ERROR messages with pretty\-printed JSON<a name="troubleshooting-athena-json-serde-hive-cursor-error"></a>

The [Hive JSON SerDe](json-serde.md#hive-json-serde) and [OpenX JSON SerDe](json-serde.md#openx-json-serde) libraries expect each JSON document to be on a single line of text with no line termination characters separating the fields in the record\. If the JSON text is in pretty print format, you may receive an error message like HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object or HIVE\_CURSOR\_ERROR: JsonParseException: Unexpected end\-of\-input: expected close marker for OBJECT when you attempt to query the table after you create it\. For more information, see [JSON Data Files](https://github.com/rcongiu/Hive-JSON-Serde#json-data-files) in the OpenX SerDe documentation on GitHub\.

### Multiple JSON records return a SELECT COUNT of 1<a name="troubleshooting-athena-multiple-json-records-return-a-select-count-of-1"></a>

If you're using the [OpenX JSON SerDe](json-serde.md#openx-json-serde), make sure that the records are separated by a newline character\. For more information, see [The SELECT COUNT query in Amazon Athena returns only one record even though the input JSON file has multiple records](http://aws.amazon.com/premiumsupport/knowledge-center/select-count-query-athena-json-records/) in the AWS Knowledge Center\.

### Cannot query a table created by a AWS Glue crawler that uses a custom JSON classifier<a name="troubleshooting-athena-cannot-query-a-table-created-by-a-glue-crawler-that-uses-a-custom-json-classifier"></a>

The Athena engine does not support [custom JSON classifiers](https://docs.aws.amazon.com/glue/latest/dg/custom-classifier.html#custom-classifier-json)\. To work around this issue, create a new table without the custom classifier\. To transform the JSON, you can use CTAS or create a view\. For example, if you are working with arrays, you can use the UNNEST option to flatten the JSON\. Another option is to use a AWS Glue ETL job that supports the custom classifier, convert the data to parquet in Amazon S3, and then query it in Athena\.

## MSCK REPAIR TABLE<a name="troubleshooting-athena-msck-repair-table"></a>

For information about MSCK REPAIR TABLE related issues, see the [Considerations and Limitations](msck-repair-table.md#msck-repair-table-considerations) and [Troubleshooting](msck-repair-table.md#msck-repair-table-troubleshooting) sections of the [MSCK REPAIR TABLE](msck-repair-table.md) page\.

## Output Issues<a name="troubleshooting-athena-output-issues"></a>

### Unable to verify/create output bucket<a name="troubleshooting-athena-unable-to-verifycreate-output-bucket"></a>

This error can occur if the specified query result location doesn't exist or if the proper permissions are not present\. For more information, see [How do I resolve the "Unable to verify/create output bucket" error in Amazon Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-output-bucket-error/) in the AWS Knowledge Center\.

### TIMESTAMP result is empty<a name="troubleshooting-athena-timestamp-result-is-empty"></a>

Athena requires the Java TIMESTAMP format\. For more information, see [When I query a table in Amazon Athena, the TIMESTAMP result is empty](http://aws.amazon.com/premiumsupport/knowledge-center/query-table-athena-timestamp-empty/) in the AWS Knowledge Center\.

### Store Athena query output in a format other than CSV<a name="troubleshooting-athena-store-athena-query-output-in-a-format-other-than-csv"></a>

By default, Athena outputs files in CSV format only\. To output the results of a `SELECT` query in a different format, you can use the `UNLOAD` statement\. For more information, see [UNLOAD](unload.md)\. You can also use a CTAS query that uses the `format` [table property](create-table-as.md#ctas-table-properties) to configure the output format\. Unlike `UNLOAD`, the CTAS technique requires the creation of a table\. For more information, see [How can I store an Athena query output in a format other than CSV, such as a compressed format?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-query-output-different-format/) in the AWS Knowledge Center\.

### The S3 location provided to save your query results is invalid<a name="troubleshooting-athena-the-s3-location-provided-to-save-your-query-results-is-invalid"></a>

You can receive this error message if your output bucket location is not in the same Region as the Region in which you run your query\. To avoid this, specify a query results location in the Region in which you run the query\. For steps, see [Specifying a Query Result Location](querying.md#query-results-specify-location)\.

## Partitioning Issues<a name="troubleshooting-athena-partitioning-issues"></a>

### MSCK REPAIR TABLE does not remove stale partitions<a name="troubleshooting-athena-msck-repair-table-does-not-remove-stale-partitions"></a>

If you delete a partition manually in Amazon S3 and then run MSCK REPAIR TABLE, you may receive the error message Partitions missing from filesystem\. This occurs because MSCK REPAIR TABLE doesn't remove stale partitions from table metadata\. Use [ALTER TABLE DROP PARTITION](alter-table-drop-partition.md) to remove the stale partitions manually\. For more information, see the "Troubleshooting" section of the [MSCK REPAIR TABLE](msck-repair-table.md) topic\.

### MSCK REPAIR TABLE failure<a name="troubleshooting-athena-msck-repair-table-failure"></a>

When a large amount of partitions \(for example, more than 100,000\) are associated with a particular table, `MSCK REPAIR TABLE` can fail due to memory limitations\. To work around this limit, use [ALTER TABLE ADD PARTITION](alter-table-add-partition.md) instead\.

### MSCK REPAIR TABLE detects partitions but doesn't add them to AWS Glue<a name="troubleshooting-athena-msck-repair-table-detects-partitions-but-doesnt-add-them-to-glue"></a>

This issue can occur if an Amazon S3 path is in camel case instead of lower case or an IAM policy doesn't allow the `glue:BatchCreatePartition` action\. For more information, see [MSCK REPAIR TABLE detects partitions in Athena but does not add them to the AWS Glue Data Catalog](http://aws.amazon.com/premiumsupport/knowledge-center/athena-aws-glue-msck-repair-table/) in the AWS Knowledge Center\.

### Partition projection ranges with the date format of dd\-MM\-yyyy\-HH\-mm\-ss or yyyy\-MM\-dd do not work<a name="troubleshooting-athena-partition-projection-ranges-with-the-date-format-of-dd-mm-yyyy-hh-mm-ss-or-yyyy-mm-dd-do-not-work"></a>

To work correctly, the date format must be set to `yyyy-MM-dd HH:00:00`\. For more information, see the Stack Overflow post [Athena Partition Projection Not Working As Expected](https://stackoverflow.com/questions/63943920/athena-partition-projection-not-working-as-expected)\.

### PARTITION BY doesn't support the BIGINT type<a name="troubleshooting-athena-partition-by-doesnt-support-the-bigint-type"></a>

Convert the data type to `string` and retry\.

### No meaningful partitions available<a name="troubleshooting-athena-no-meaningful-partitions-available"></a>

This error message usually means the partition settings have been corrupted\. To resolve this issue, drop the table and create a table with new partitions\.

### Partition projection does not work in conjunction with range partitions<a name="troubleshooting-athena-partition-projection-does-not-work-in-conjunction-with-range-partitions"></a>

Check that the time range unit [projection\.*<columnName>*\.interval\.unit](partition-projection-supported-types.md#partition-projection-date-type) matches the delimiter for the partitions\. For example, if partitions are delimited by days, then a range unit of hours will not work\.

### HIVE\_UNKNOWN\_ERROR: Unable to create input format<a name="troubleshooting-athena-hive_unknown_error-unable-to-create-input-format-1"></a>

One or more of the glue partitions are declared in a different format as each glue partition has their own specific input format independently\. Please check how your partitions are defined in AWS Glue\.

### HIVE\_PARTITION\_SCHEMA\_MISMATCH<a name="troubleshooting-athena-hive_partition_schema_mismatch"></a>

If the schema of a partition differs from the schema of the table, a query can fail with the error message HIVE\_PARTITION\_SCHEMA\_MISMATCH\. For more information, see [Syncing Partition Schema to Avoid "HIVE\_PARTITION\_SCHEMA\_MISMATCH"](glue-best-practices.md#schema-syncing)\.

### SemanticException table is not partitioned but partition spec exists<a name="troubleshooting-athena-semanticexception-table-is-not-partitioned-but-partition-spec-exists"></a>

This error can occur when no partitions were defined in the `CREATE TABLE` statement\. For more information, see [How can I troubleshoot the error "FAILED: SemanticException table is not partitioned but partition spec exists" in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-failed-semanticexception-table/) in the AWS Knowledge Center\.

### Zero records returned from partitioned data<a name="troubleshooting-athena-zero-records-returned-from-partitioned-data"></a>

This issue can occur for a variety of reasons\. For possible causes and resolutions, see [I created a table in Amazon Athena with defined partitions, but when I query the table, zero records are returned](http://aws.amazon.com/premiumsupport/knowledge-center/athena-empty-results/) in the AWS Knowledge Center\.

## Permissions<a name="troubleshooting-athena-permissions"></a>

### Access Denied Error when querying Amazon S3<a name="troubleshooting-athena-access-denied-error-when-querying-amazon-s3"></a>

This can occur when you don't have permission to read the data in the bucket, permission to write to the results bucket, or the Amazon S3 path contains a Region endpoint like `us-east-1.amazonaws.com`\. For more information, see [When I run an Athena query, I get an "Access Denied" error](http://aws.amazon.com/premiumsupport/knowledge-center/access-denied-athena/) in the AWS Knowledge Center\.

### Access Denied with Status Code: 403 error when running DDL queries on encrypted data in Amazon S3<a name="troubleshooting-athena-access-denied-error-when-querying-amazon-s3-encrypted"></a>

When you may receive the error message Access Denied \(Service: Amazon S3; Status Code: 403; Error Code: AccessDenied; Request ID: *<request\_id>*\) if the following conditions are true:

1. You run a DDL query like `ALTER TABLE ADD PARTITION` or `MSCK REPAIR TABLE`\.

1. You have a bucket that has [default encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-bucket-encryption.html) configured to use `SSE-S3`\.

1. The bucket also has a bucket policy like the following that forces `PutObject` requests to specify the `PUT` headers `"s3:x-amz-server-side-encryption": "true"` and `"s3:x-amz-server-side-encryption": "AES256"`\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Deny",
               "Principal": "*",
               "Action": "s3:PutObject",
               "Resource": "arn:aws:s3:::<resource-name>/*",
               "Condition": {
                   "Null": {
                       "s3:x-amz-server-side-encryption": "true"
                   }
               }
           },
           {
               "Effect": "Deny",
               "Principal": "*",
               "Action": "s3:PutObject",
               "Resource": "arn:aws:s3:::<resource-name>/*",
               "Condition": {
                   "StringNotEquals": {
                       "s3:x-amz-server-side-encryption": "AES256"
                   }
               }
           }
       ]
   }
   ```

In a case like this, the recommended solution is to remove the bucket policy like the one above given that the bucket's default encryption is already present\.

### Access Denied with Status Code: 403 when querying an Amazon S3 bucket in another account<a name="troubleshooting-athena-access-denied-with-status-code-403-when-querying-an-amazon-s3-bucket-in-another-account"></a>

This error can occur when you try to query logs written by another AWS service and the second account is the bucket owner but does not own the objects in the bucket\. For more information, see [I get the Amazon S3 Exception "Access Denied with Status Code: 403" in Amazon Athena when I query a bucket in another account](http://aws.amazon.com/premiumsupport/knowledge-center/athena-access-denied-status-code-403/) in the AWS Knowledge Center or watch the Knowledge Center [video](https://youtu.be/0j6U4gv2Os8)\.

### Use IAM role credentials to connect to the Athena JDBC driver<a name="troubleshooting-athena-use-IAM-role-credentials-to-connect-to-the-athena-jdbc-driver"></a>

You can retrieve a role's temporary credentials to authenticate the [JDBC connection to Athena](connect-with-jdbc.md)\. Temporary credentials have a maximum lifespan of 12 hours\. For more information, see [How can I use my IAM role credentials or switch to another IAM role when connecting to Athena using the JDBC driver?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-iam-jdbc-driver/) in the AWS Knowledge Center\.

## Query Syntax Issues<a name="troubleshooting-athena-query-syntax-issues"></a>

### FAILED: NullPointerException Name is null<a name="troubleshooting-athena-nullpointerexception-name-is-null"></a>

If you use the AWS Glue [CreateTable](https://docs.aws.amazon.com/glue/latest/webapi/API_CreateTable.html) API operation or the AWS CloudFormation [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-table.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-table.html) template to create a table for use in Athena without specifying the `TableType` property and then run a DDL query like `SHOW CREATE TABLE` or `MSCK REPAIR TABLE`, you can receive the error message FAILED: NullPointerException Name is null\. 

To resolve the error, specify a value for the [TableInput](https://docs.aws.amazon.com/glue/latest/webapi/API_TableInput.html) `TableType` attribute as part of the AWS Glue `CreateTable` API call or [AWS CloudFormation template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-glue-table-tableinput.html)\. Possible values for `TableType` include `EXTERNAL_TABLE` or `VIRTUAL_VIEW`\.

This requirement applies only when you create a table using the AWS Glue `CreateTable` API operation or the `AWS::Glue::Table` template\. If you create a table for Athena by using a DDL statement or an AWS Glue crawler, the `TableType` property is defined for you automatically\. 

### Function not registered<a name="troubleshooting-athena-function-not-registered"></a>

This error occurs when you try to use a function that Athena doesn't support\. For a list of functions that Athena supports, see [Functions in Amazon Athena](presto-functions.md) or run the `SHOW FUNCTIONS` statement in the Query Editor\. You can also write your own [user defined function \(UDF\)](querying-udf.md)\. For more information, see [How do I resolve the "function not registered" syntax error in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-syntax-function-not-registered/) in the AWS Knowledge Center\.

### GENERIC\_INTERNAL\_ERROR Exceptions<a name="troubleshooting-athena-generic-internal-error"></a>

`GENERIC_INTERNAL_ERROR` exceptions can have a variety of causes, including the following:
+ **GENERIC\_INTERNAL\_ERROR: null** – You might see this exception under either of the following conditions:
  + You have a schema mismatch between the data type of a column in table definition and the actual data type of the dataset\.
  + You are running a `CREATE TABLE AS SELECT` \(CTAS\) query with inaccurate syntax\.
+ **GENERIC\_INTERNAL\_ERROR: parent builder is null** – You might see this exception when you query a table with columns of data type `array`, and you are using the OpenCSVSerDe library\. The OpenCSVSerde format doesn't support the `array` data type\.
+ **GENERIC\_INTERNAL\_ERROR: Value exceeds MAX\_INT** – You might see this exception when the source data column is defined with the data type `INT` and has a numeric value greater than 2,147,483,647\.
+ **GENERIC\_INTERNAL\_ERROR: Value exceeds MAX\_BYTE** – You might see this exception when the source data column has a numeric value exceeding the allowable size for the data type `BYTE`\. The data type `BYTE` is equivalent to `TINYINT`\. `TINYINT` is an 8\-bit signed integer in two’s complement format with a minimum value of \-128 and a maximum value of 127\.
+ **GENERIC\_INTERNAL\_ERROR: Number of partition values does not match number of filters** – You might see this exception if you have inconsistent partitions on Amazon Simple Storage Service\(Amazon S3\) data\. You might have inconsistent partitions under either of the following conditions:
  + Partitions on Amazon S3 have changed \(example: new partitions were added\)\.
  + The number of partition columns in the table do not match those in the partition metadata\.

For more detailed information about each of these errors, see [How do I resolve the error "GENERIC\_INTERNAL\_ERROR" when I query a table in Amazon Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-generic-internal-error/) in the AWS Knowledge Center\.

### Number of matching groups doesn't match the number of columns<a name="troubleshooting-athena-number-of-matching-groups-doesnt-match-the-number-of-columns"></a>

This error occurs when you use the [Regex SerDe](regex-serde.md) in a CREATE TABLE statement and the number of regex matching groups doesn't match the number of columns that you specified for the table\. For more information, see [How do I resolve the RegexSerDe error "Number of matching groups doesn't match the number of columns" in Amazon Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/regexserde-error-athena-matching-groups/) in the AWS Knowledge Center\.

### queryString failed to satisfy constraint: Member must have length less than or equal to 262144<a name="troubleshooting-athena-querystring-failed-to-satisfy-constraint-member-must-have-length-less-than-or-equal-to-262144"></a>

The maximum query string length in Athena \(262,144 bytes\) is not an adjustable quota\. AWS Support can't increase the quota for you, but you can work around the issue by splitting long queries into smaller ones\. For more information, see [How can I increase the maximum query string length in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-query-string-length/) in the AWS Knowledge Center\.

### SYNTAX\_ERROR: Column cannot be resolved<a name="troubleshooting-athena-syntax_error-column-cannot-be-resolved"></a>

This error can occur when you query a table created by an AWS Glue crawler from a UTF\-8 encoded CSV file that has a byte order mark \(BOM\)\. AWS Glue doesn't recognize the BOMs and changes them to question marks, which Amazon Athena doesn't recognize\. The solution is to remove the question mark in Athena or in AWS Glue\.

## Throttling Issues<a name="troubleshooting-athena-throttling-issues"></a>

If your queries exceed the limits of dependent services such as Amazon S3, AWS KMS, AWS Glue, or AWS Lambda, the following messages can be expected\. To resolve these issues, reduce the number of concurrent calls that originate from the same account\.


****  

| Service | Error Message | 
| --- | --- | 
| AWS Glue | AWSGlueException: Rate exceeded\. | 
| AWS KMS | You have exceeded the rate at which you may call KMS\. Reduce the frequency of your calls\. | 
| AWS Lambda |  Rate exceeded TooManyRequestsException  | 
| Amazon S3 | AmazonS3Exception: Please reduce your request rate\. | 

## Views<a name="troubleshooting-athena-views"></a>

### Views created in Apache Hive shell do not work in Athena<a name="troubleshooting-athena-views-created-in-hive-shell-do-not-work-in-athena"></a>

Because of their fundamentally different implementations, views created in Apache Hive shell are not compatible with Athena\. To resolve this issue, re\-create the views in Athena\.

### View is stale; it must be re\-created<a name="troubleshooting-athena-view-is-stale-it-must-be-re-created"></a>

You can receive this error if the table that underlies a view has altered or dropped\. The resolution is to recreate the view\. For more information, see [How can I resolve the "View is stale; it must be re\-created" error in Athena?](http://aws.amazon.com/premiumsupport/knowledge-center/athena-view-is-stale-error/) in the AWS Knowledge Center\.

## Workgroups<a name="troubleshooting-athena-workgroups"></a>

For information on troubleshooting workgroup issues, see [Troubleshooting Workgroups](workgroups-troubleshooting.md)\.

## Additional Resources<a name="troubleshooting-athena-additional-resources"></a>

The following pages provide additional information for troubleshooting issues with Amazon Athena\.
+ [Service Quotas](service-limits.md)
+ [Considerations and Limitations for SQL Queries in Amazon Athena](other-notable-limitations.md)
+ [Unsupported DDL](unsupported-ddl.md)
+ [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)
+ [Data Types in Amazon Athena](data-types.md)
+ [Supported SerDes and Data Formats](supported-serdes.md)
+ [Athena Compression Support](compression-formats.md)
+ [Reserved Keywords](reserved-words.md)
+ [Troubleshooting Workgroups](workgroups-troubleshooting.md)

The following AWS resources can also be of help:
+  [Athena topics in the AWS Knowledge Center](https://aws.amazon.com/premiumsupport/knowledge-center/#Amazon_Athena) 
+  [Amazon Athena questions on AWS re:Post](https://repost.aws/tags/TA78iVOM7gR62_QqDe2-CmiA/amazon-athena)
+  [Athena posts in the AWS Big Data Blog](http://aws.amazon.com/blogs/big-data/tag/amazon-athena/) 

Troubleshooting often requires iterative query and discovery by an expert or from a community of helpers\. If you continue to experience issues after trying the suggestions on this page, contact AWS Support \(in the AWS Management Console, click **Support**, **Support Center**\) or ask a question on [AWS re:Post](https://repost.aws/tags/TA78iVOM7gR62_QqDe2-CmiA/amazon-athena) using the **Amazon Athena** tag\.