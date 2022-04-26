# DESCRIBE<a name="describe-table"></a>

Shows one or more columns, including partition columns, for the specified table\. This command is useful for examining the attributes of complex columns\.

## Synopsis<a name="synopsis"></a>

```
DESCRIBE [EXTENDED | FORMATTED] [db_name.]table_name [PARTITION partition_spec] [col_name ( [.field_name] | [.'$elem$'] | [.'$key$'] | [.'$value$'] )]
```

**Important**  
The syntax for this statement is `DESCRIBE table_name`, not `DESCRIBE TABLE table_name`\. Using the latter syntax results in the error message FAILED: SemanticException \[Error 10001\]: Table not found table\. 

## Parameters<a name="parameters"></a>

**\[EXTENDED \| FORMATTED\]**  
Determines the format of the output\. Omitting these parameters shows column names and their corresponding data types, including partition columns, in tabular format\. Specifying `FORMATTED` not only shows column names and data types in tabular format, but also detailed table and storage information\. `EXTENDED` shows column and data type information in tabular format, and detailed metadata for the table in Thrift serialized form\. This format is less readable and is useful primarily for debugging\.

**\[PARTITION partition\_spec\]**  
If included, lists the metadata for the partition specified by `partition_spec`, where `partition_spec` is in the format `(partition_column = partition_col_value, partition_column = partition_col_value, ...)`\.

**\[col\_name \( \[\.field\_name\] \| \[\.'$elem$'\] \| \[\.'$key$'\] \| \[\.'$value$'\] \)\* \]**  
Specifies the column and attributes to examine\. You can specify `.field_name` for an element of a struct, `'$elem$'` for array element, `'$key$'` for a map key, and `'$value$'` for map value\. You can specify this recursively to further explore the complex column\.

### Examples<a name="examples"></a>

```
DESCRIBE orders
```

```
DESCRIBE FORMATTED mydatabase.mytable PARTITION (part_col = 100) columnA;
```

The following query and output shows column and data type information from an `impressions` table based on Amazon EMR sample data\.

```
DESCRIBE impressions
```

```
requestbegintime          string                                         from deserializer   
adid                      string                                         from deserializer   
impressionid              string                                         from deserializer   
referrer                  string                                         from deserializer   
useragent                 string                                         from deserializer   
usercookie                string                                         from deserializer   
ip                        string                                         from deserializer   
number                    string                                         from deserializer   
processid                 string                                         from deserializer   
browsercokie              string                                         from deserializer   
requestendtime            string                                         from deserializer   
timers                    struct<modellookup:string,requesttime:string>  from deserializer   
threadid                  string                                         from deserializer   
hostname                  string                                         from deserializer   
sessionid                 string                                         from deserializer   
dt                        string

# Partition Information
# col_name                data_type                 comment             

dt                        string
```

The following example query and output show the result for the same table when the `FORMATTED` option is used\.

```
DESCRIBE FORMATTED impressions
```

```
requestbegintime          string                                         from deserializer
adid                      string                                         from deserializer
impressionid              string                                         from deserializer
referrer                  string                                         from deserializer
useragent                 string                                         from deserializer
usercookie                string                                         from deserializer
ip                        string                                         from deserializer
number                    string                                         from deserializer
processid                 string                                         from deserializer
browsercokie              string                                         from deserializer
requestendtime            string                                         from deserializer
timers                    struct<modellookup:string,requesttime:string>  from deserializer
threadid                  string                                         from deserializer
hostname                  string                                         from deserializer
sessionid                 string                                         from deserializer
dt                        string

# Partition Information
# col_name                data_type                 comment

dt                        string

# Detailed Table Information
Database:                 sampledb
Owner:                    hadoop
CreateTime:               Thu Apr 23 02:55:21 UTC 2020
LastAccessTime:           UNKNOWN
Protect Mode:             None
Retention:                0
Location:                 s3://us-east-1.elasticmapreduce/samples/hive-ads/tables/impressions
Table Type:               EXTERNAL_TABLE
Table Parameters:
        EXTERNAL                  TRUE
        transient_lastDdlTime     1587610521

# Storage Information
SerDe Library:                         org.openx.data.jsonserde.JsonSerDe
InputFormat:                           org.apache.hadoop.mapred.TextInputFormat
OutputFormat:                          org.apache.hadoop.hive.ql.io.IgnoreKeyTextOutputFormat
Compressed:                            No
Num Buckets:                           -1
Bucket Columns:                        []
Sort Columns:                          []
Storage Desc Params:
        paths                                  requestbegintime, adid, impressionid, referrer, useragent, usercookie, ip
        serialization.format                   1
```

The following example query and output show the result for the same table when the `EXTENDED` option is used\. The detailed table information is output on a single line, but is formatted here for readability\.

```
DESCRIBE EXTENDED impressions
```

```
requestbegintime          string                                         from deserializer
adid                      string                                         from deserializer
impressionid              string                                         from deserializer
referrer                  string                                         from deserializer
useragent                 string                                         from deserializer
usercookie                string                                         from deserializer
ip                        string                                         from deserializer
number                    string                                         from deserializer
processid                 string                                         from deserializer
browsercokie              string                                         from deserializer
requestendtime            string                                         from deserializer
timers                    struct<modellookup:string,requesttime:string>  from deserializer
threadid                  string                                         from deserializer
hostname                  string                                         from deserializer
sessionid                 string                                         from deserializer
dt                        string

# Partition Information
# col_name                data_type                 comment

dt                        string

Detailed Table Information       Table(tableName:impressions, dbName:sampledb, owner:hadoop, createTime:1587610521, 
lastAccessTime:0, retention:0, sd:StorageDescriptor(cols:[FieldSchema(name:requestbegintime, type:string, comment:null), 
FieldSchema(name:adid, type:string, comment:null), FieldSchema(name:impressionid, type:string, comment:null), 
FieldSchema(name:referrer, type:string, comment:null), FieldSchema(name:useragent, type:string, comment:null), 
FieldSchema(name:usercookie, type:string, comment:null), FieldSchema(name:ip, type:string, comment:null), 
FieldSchema(name:number, type:string, comment:null), FieldSchema(name:processid, type:string, comment:null), 
FieldSchema(name:browsercokie, type:string, comment:null), FieldSchema(name:requestendtime, type:string, comment:null), 
FieldSchema(name:timers, type:struct<modellookup:string,requesttime:string>, comment:null), FieldSchema(name:threadid, 
type:string, comment:null), FieldSchema(name:hostname, type:string, comment:null), FieldSchema(name:sessionid, 
type:string, comment:null)], location:s3://us-east-1.elasticmapreduce/samples/hive-ads/tables/impressions, 
inputFormat:org.apache.hadoop.mapred.TextInputFormat, 
outputFormat:org.apache.hadoop.hive.ql.io.IgnoreKeyTextOutputFormat, compressed:false, numBuckets:-1, 
serdeInfo:SerDeInfo(name:null, serializationLib:org.openx.data.jsonserde.JsonSerDe, parameters:{serialization.format=1, 
paths=requestbegintime, adid, impressionid, referrer, useragent, usercookie, ip}), bucketCols:[], sortCols:[], parameters:{}, 
skewedInfo:SkewedInfo(skewedColNames:[], skewedColValues:[], skewedColValueLocationMaps:{}), 
storedAsSubDirectories:false), partitionKeys:[FieldSchema(name:dt, type:string, comment:null)], 
parameters:{EXTERNAL=TRUE, transient_lastDdlTime=1587610521}, viewOriginalText:null, viewExpandedText:null, 
tableType:EXTERNAL_TABLE)
```