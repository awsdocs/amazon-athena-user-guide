# Supported Data Types<a name="lf-governed-tables-supported-data-types"></a>

Governed tables currently support the following data types:

```
array 
bigint 
boolean 
char 
date (YYYY-MM-DD) 
decimal 
double 
float 
integer 
long 
map 
smallint 
string 
struct 
timestamp 
varchar
```

**Note**  
Currently, support for the `double` and `float` types in governed tables is limited\. For `float`, Athena supports only up to 6 \(instead of 9\) decimals of precision\. For `double`, Athena supports only up to 15 \(instead of 17\) digits of precision\. 

For more information about data types in Athena, see [Data Types in Amazon Athena](data-types.md)\.