# OpenX JSON SerDe<a name="openx-json-serde"></a>

Like the Hive JSON SerDe, you can use the OpenX JSON to process JSON data\. The data are also represented as single\-line strings of JSON\-encoded text separated by a new line\. Like the Hive JSON SerDe, the OpenX JSON SerDe does not allow duplicate keys in `map` or `struct` key names\. 

**Note**  
The SerDe expects each JSON document to be on a single line of text with no line termination characters separating the fields in the record\. If the JSON text is in pretty print format, you may receive an error message like HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object or HIVE\_CURSOR\_ERROR: JsonParseException: Unexpected end\-of\-input: expected close marker for OBJECT when you attempt to query the table after you create it\. For more information, see [JSON Data Files](https://github.com/rcongiu/Hive-JSON-Serde#json-data-files) in the OpenX SerDe documentation on GitHub\. 

## Optional properties<a name="openx-json-serde-optional-properties"></a>

Unlike the Hive JSON SerDe, the OpenX JSON SerDe also has the following optional SerDe properties that can be useful for addressing inconsistencies in data\.

**ignore\.malformed\.json**  
Optional\. When set to `TRUE`, lets you skip malformed JSON syntax\. The default is `FALSE`\.

**dots\.in\.keys**  
Optional\. The default is `FALSE`\. When set to `TRUE`, allows the SerDe to replace the dots in key names with underscores\. For example, if the JSON dataset contains a key with the name `"a.b"`, you can use this property to define the column name to be `"a_b"` in Athena\. By default \(without this SerDe\), Athena does not allow dots in column names\.

**case\.insensitive**  
Optional\. The default is `TRUE`\. When set to `TRUE`, the SerDe converts all uppercase columns to lowercase\.   
To use case\-sensitive key names in your data, use `WITH SERDEPROPERTIES ("case.insensitive"= FALSE;)`\. Then, for every key that is not already all lowercase, provide a mapping from the column name to the property name using the following syntax:  

```
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
WITH SERDEPROPERTIES ("case.insensitive" = "FALSE", "mapping.userid" = "userId")
```
If you have two keys like `URL` and `Url` that are the same when they are in lowercase, an error like the following can occur:  
HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object \- JSONException: Duplicate key "url"  
To resolve this, set the `case.insensitive` property to `FALSE` and map the keys to different names, as in the following example:  

```
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
WITH SERDEPROPERTIES ("case.insensitive" = "FALSE", "mapping.url1" = "URL", "mapping.url2" = "Url")
```

**mapping**  
Optional\. Maps column names to JSON keys that aren't identical to the column names\. The `mapping` parameter is useful when the JSON data contains keys that are [keywords](reserved-words.md)\. For example, if you have a JSON key named `timestamp`, use the following syntax to map the key to a column named `ts`:  

```
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
WITH SERDEPROPERTIES ("mapping.ts" = "timestamp")
```
**Mapping nested field names with colons to Hive\-compatible names**  
If you have a field name with colons inside a `struct`, you can use the `mapping` property to map the field to a Hive\-compatible name\. For example, if your column type definitions contain `my:struct:field:string`, you can map the definition to `my_struct_field:string` by including the following entry in `WITH SERDEPROPERTIES`:

```
("mapping.my_struct_field" = "my:struct:field")
```
The following example shows the corresponding `CREATE TABLE` statement\.  

```
CREATE EXTERNAL TABLE colon_nested_field (
item struct<my_struct_field:string>)
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
WITH SERDEPROPERTIES ("mapping.my_struct_field" = "my:struct:field")
```

## Example: advertising data<a name="openx-json-serde-ad-data-example"></a>

The following example DDL statement uses the OpenX JSON SerDe to create a table based on the same sample online advertising data used in the example for the Hive JSON SerDe\. In the `LOCATION` clause, replace *myregion* with the region identifier where you run Athena\.

```
CREATE EXTERNAL TABLE impressions (
    requestbegintime string,
    adid string,
    impressionId string,
    referrer string,
    useragent string,
    usercookie string,
    ip string,
    number string,
    processid string,
    browsercokie string,
    requestendtime string,
    timers struct<
       modellookup:string, 
       requesttime:string>,
    threadid string, 
    hostname string,
    sessionid string
)   PARTITIONED BY (dt string)
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
with serdeproperties ( 'paths'='requestbegintime, adid, impressionid, referrer, useragent, usercookie, ip' )
LOCATION 's3://myregion.elasticmapreduce/samples/hive-ads/tables/impressions';
```

## Example: deserializing nested JSON<a name="nested-json-serde-example"></a>

You can use the JSON SerDes to parse more complex JSON\-encoded data\. This requires using `CREATE TABLE` statements that use `struct` and `array` elements to represent nested structures\. 

The following example creates an Athena table from JSON data that has nested structures\. To parse JSON\-encoded data in Athena, make sure that each JSON document is on its own line, separated by a new line\. 

This example presumes JSON\-encoded data that has the following structure:

```
{
"DocId": "AWS",
"User": {
        "Id": 1234,
        "Username": "bob1234", 
        "Name": "Bob",
"ShippingAddress": {
"Address1": "123 Main St.",
"Address2": null,
"City": "Seattle",
"State": "WA"
   },
"Orders": [
   {
     "ItemId": 6789,
     "OrderDate": "11/11/2017" 
   },
   {
     "ItemId": 4352,
     "OrderDate": "12/12/2017"
   }
  ]
 }
}
```

The following `CREATE TABLE` statement uses the [Openx\-JsonSerDe](https://github.com/rcongiu/Hive-JSON-Serde) with the `struct` and `array` collection data types to establish groups of objects\. Each JSON document is listed on its own line, separated by a new line\. To avoid errors, the data being queried does not include duplicate keys in `struct` or map key names\.

```
CREATE external TABLE complex_json (
   docid string,
   `user` struct<
               id:INT,
               username:string,
               name:string,
               shippingaddress:struct<
                                      address1:string,
                                      address2:string,
                                      city:string,
                                      state:string
                                      >,
               orders:array<
                            struct<
                                 itemid:INT,
                                  orderdate:string
                                  >
                              >
               >
   )
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://mybucket/myjsondata/';
```