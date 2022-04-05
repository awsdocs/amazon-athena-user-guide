# March 18, 2022<a name="release-note-2022-03-18"></a>

Published on 2022\-03\-18

Athena announces the following fixes and improvements\.
+ **Dynamic filtering** – [Dynamic filtering](release-note-2021-07-30.md) has been improved for integer columns by efficiently applying the filter to each record of a corresponding table\. 
+ **Iceberg** – Fixed an issue that caused failures when writing Iceberg Parquet files larger than 2GB\. 
+ **Uncompressed output** – [CREATE TABLE](create-table.md) statements now support writing uncompressed files\. To write uncompressed files, use the following syntax: 
  + CREATE TABLE \(text file or JSON\) – In `TBLPROPERTIES`, specify `write.compression = NONE`\.
  + CREATE TABLE \(Parquet\) – In `TBLPROPERTIES`, specify `parquet.compression = UNCOMPRESSED`\.
  + CREATE TABLE \(ORC\) – In `TBLPROPERTIES`, specify `orc.compress = NONE`\.
+ **Compression** – Fixed an issue with inserts for text file tables that created files compressed in one format but used another compression format file extension when non\-default compression methods were used\. 
+ **Avro** – Fixed issues that occurred when reading decimals of the fixed type from Avro files\. 