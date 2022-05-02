# February 20, 2017<a name="release-note-2017-02-20"></a>

Published on *2017\-02\-20*

Added support for AvroSerDe and OpenCSVSerDe, US East \(Ohio\) Region, and bulk editing columns in the console wizard\. Improved performance on large Parquet tables\.

## Features<a name="release-note-2017-02-20-features"></a>
+ **Introduced support for new SerDes:**
  + [Avro SerDe](avro-serde.md)
  + [OpenCSVSerDe for processing CSV](csv-serde.md)
+ **US East \(Ohio\)** Region \(**us\-east\-2**\) launch\. You can now run queries in this region\.
+ You can now use the **Create Table From S3 bucket data** form to define table schema in bulk\. In the query editor, choose **Create**, **S3 bucket data**, and then choose **Bulk add columns** in the **Column details** section\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/bulk-add.png)

  Type name value pairs in the text box and choose **Add**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/bulk_column.png)

## Improvements<a name="release-note-2017-02-20-improvements"></a>
+ Improved performance on large Parquet tables\.