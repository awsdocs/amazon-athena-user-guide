# February 20, 2017<a name="release-note-2017-02-20"></a>

Published on *2017\-02\-20*

Added support for AvroSerDe and OpenCSVSerDe, US East \(Ohio\) region, and bulk editing columns in the console wizard\. Improved performance on large Parquet tables\.

## Features<a name="release-note-2017-02-20-features"></a>

+ **Introduced support for new SerDes:**

  + [Avro SerDe](avro.md)

  + [OpenCSVSerDe for Processing CSV](csv.md)

+ **US East \(Ohio\)** region \(**us\-east\-2**\) launch\. You can now run queries in this region\.

+ You can now use the **Add Table** wizard to define table schema in bulk\. Choose **Catalog Manager**, **Add table**, and then choose **Bulk add columns** as you walk through the steps to define the table\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/bulk_add.png)

  Type name value pairs in the text box and choose **Add**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/bulk_column.png)

## Improvements<a name="release-note-2017-02-20-improvements"></a>

+ Improved performance on large Parquet tables\.