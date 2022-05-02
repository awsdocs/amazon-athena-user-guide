# Using CTAS and INSERT INTO to create Amazon Ion tables<a name="ion-serde-using-ctas-and-insert-into-to-create-ion-tables"></a>

You can use the `CREATE TABLE AS SELECT` \(CTAS\) and `INSERT INTO` statements to copy or insert data from a table into a new table in Amazon Ion format in Athena\.

In a CTAS query, specify `format='ION'` in the `WITH` clause, as in the following example\.

```
CREATE TABLE new_table
WITH (format='ION')
AS SELECT * from existing_table
```

By default, Athena serializes Amazon Ion results in [Ion binary format](https://amzn.github.io/ion-docs/docs/binary.html), but you can also use text format\. To use text format, specify `ion_encoding = 'TEXT'` in the CTAS `WITH` clause, as in the following example\.

```
CREATE TABLE new_table
WITH (format='ION', ion_encoding = 'TEXT')
AS SELECT * from existing_table
```

For more information about Amazon Ion specific properties in the CTAS `WITH` clause, see the following section\.

## CTAS WITH clause Amazon Ion properties<a name="ion-serde-ctas-with-clause-properties"></a>

In a CTAS query, you can use the `WITH` clause to specify the Amazon Ion format and optionally specify the Amazon Ion encoding and/or write compression algorithm to use\.

**format**  
You can specify the `ION` keyword as the format option in the `WITH` clause of a CTAS query\. When you do so, the table that you create uses the format that you specify for `IonInputFormat` for reads, and it serializes data in the format that you specify for `IonOutputFormat`\.  
The following example specifies that the CTAS query use Amazon Ion format\.  

```
WITH (format='ION')
```

**ion\_encoding**  
Optional  
Default: `BINARY`  
Values: `BINARY`, `TEXT`  
Specifies whether data is serialized in Amazon Ion binary format or Amazon Ion text format\. The following example specifies Amazon Ion text format\.  

```
WITH (format='ION', ion_encoding='TEXT')
```

**write\_compression**  
Optional  
Default: `GZIP`  
Values: `GZIP`, `ZSTD`, `BZIP2`, `SNAPPY`, `NONE`  
Specifies the compression algorithm to use to compress output files\.  
The following example specifies that the CTAS query write its output in Amazon Ion format using the [Zstandard](https://facebook.github.io/zstd/) compression algorithm\.  

```
WITH (format='ION', write_compression = 'ZSTD')       
```
For information about using compression in Athena, see [Athena compression support](compression-formats.md)\. 

For information about other CTAS properties in Athena, see [CTAS table properties](create-table-as.md#ctas-table-properties)\.