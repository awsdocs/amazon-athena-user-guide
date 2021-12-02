# Supported Data Types<a name="querying-iceberg-supported-data-types"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

In preview, Athena can query Iceberg tables that contain the following data types:

```
binary
boolean
date
decimal
double
float
int
list
long
map
string
struct
timestamp without time zone
```

For more information about Iceberg table types, see the [schemas page for Iceberg](https://iceberg.apache.org/#schemas/) in the Apache documentation\.

The following table shows the relationship between Athena data types and Iceberg table data types\.


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/querying-iceberg-supported-data-types.html)

For more information about data types in Athena, see [Data Types in Amazon Athena](data-types.md)\.