# JSON SerDe Libraries<a name="json"></a>

In Athena, you can use two SerDe libraries for processing JSON files:

+ The native Hive JSON SerDe 

+ The OpenX JSON SerDe 

This SerDe is used to process JSON documents, most commonly events\. These events are represented as blocks of JSON\-encoded text separated by a new line\. The JsonSerDe is also capable of parsing more complex JSON documents with nested structures\. However this requires a matching DDL representing the complex data types\. There are only two properties for the JsonSerDe: `ignore.malformed.json`, which is self\-explanatory, and `dots.in.keys`, which can be set to true or false and determines if there are dots in the name of the keys that will be replaced with underscores by the SerDe\.

## SerDe Names<a name="serde-names"></a>

 [Hive\-JsonSerDe](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-JSON) 

 [Openx\-JsonSerDe](https://github.com/rcongiu/Hive-JSON-Serde) 

## Library Names<a name="library-names"></a>

Use one of the following:

 [org\.apache\.hive\.hcatalog\.data\.JsonSerDe](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-JSON) 

 [org\.openx\.data\.jsonserde\.JsonSerDe](https://github.com/rcongiu/Hive-JSON-Serde) 

## Hive JSON SerDe<a name="hivejson"></a>

The Hive JSON SerDe is used to process JSON documents, most commonly events\. These events are represented as blocks of JSON\-encoded text separated by a new line\.

You can also use the Hive JSONSerDe to parse more complex JSON documents with nested structures\. See Example: Deserializing Nested JSON 

This SerDe has two useful optional properties you can specify when creating tables in Athena, to help deal with inconsistencies in the data:

+  `'ignore.malformed.json'` if set to TRUE, lets you skip malformed JSON syntax\.

+  `'dots.in.keys'` if set to TRUE, specifies that the names of the keys include dots and replaces them with underscores\.

**Note**  
You can query data in regions other than the region where you run Athena\. Standard inter\-region data transfer rates for Amazon S3 apply in addition to standard Athena charges\. To reduce data transfer charges, replace *myregion* in `s3://athena-examples-myregion/path/to/data/` with the region identifier where you run Athena, for example, `s3://athena-examples-us-east-1/path/to/data/`\.

The following DDL statement uses the Hive JsonSerDe:

```
CREATE EXTERNAL TABLE impressions (
    requestBeginTime string,
    adId string,
    impressionId string,
    referrer string,
    userAgent string,
    userCookie string,
    ip string,
    number string,
    processId string,
    browserCookie string,
    requestEndTime string,
    timers struct<modelLookup:string, requestTime:string>,
    threadId string, hostname string,
    sessionId string
)   PARTITIONED BY (dt string)
ROW FORMAT  serde 'org.apache.hive.hcatalog.data.JsonSerDe'
with serdeproperties ( 'paths'='requestBeginTime, adId, impressionId, referrer, userAgent, userCookie, ip' )
LOCATION 's3://myregion.elasticmapreduce/samples/hive-ads/tables/impressions';
```

## OpenX JSON SerDe<a name="openxjson"></a>

The following DDL statement uses the OpenX SerDe:

```
CREATE EXTERNAL TABLE impressions (
    requestBeginTime string,
    adId string,
    impressionId string,
    referrer string,
    userAgent string,
    userCookie string,
    ip string,
    number string,
    processId string,
    browserCookie string,
    requestEndTime string,
    timers struct<modelLookup:string, requestTime:string>,
    threadId string, hostname string,
    sessionId string
)   PARTITIONED BY (dt string)
ROW FORMAT  serde 'org.openx.data.jsonserde.JsonSerDe'
with serdeproperties ( 'paths'='requestBeginTime, adId, impressionId, referrer, userAgent, userCookie, ip' )
LOCATION 's3://myregion.elasticmapreduce/samples/hive-ads/tables/impressions';
```

## Example: Deserializing Nested JSON<a name="nested-json-serde-example"></a>

JSON data can be challenging to deserialize when creating a table in Athena\. When dealing with complex nested JSON, there are common issues you may encounter\. For more information about these issues and troubleshooting practices, see the AWS Knowledge Center Article [I receive errors when I try to read JSON data in Amazon Athena](https://aws.amazon.com/premiumsupport/knowledge-center/error-json-athena/)\. For more information about common scenarios and query tips, see [Create Tables in Amazon Athena from Nested JSON and Mappings Using JSONSerDe](http://aws.amazon.com/blogs/big-data/create-tables-in-amazon-athena-from-nested-json-and-mappings-using-jsonserde/)\.

The following example demonstrates a simple approach to creating an Athena table from a nested JSON file\. This example presumes a JSON file with the following structure:

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

The following `CREATE TABLE` command uses the [Openx\-JsonSerDe](https://github.com/rcongiu/Hive-JSON-Serde) with collection data types like `struct` and `array` to establish groups of objects\.

```
CREATE external TABLE complex_json (
   DocId string,
   `USER` struct<Id:INT,
               Username:string,
               Name:string,
               ShippingAddress:struct<Address1:string,
                                      Address2:string,
                                      City:string,
                                      State:string>,
               Orders:array<struct<ItemId:INT,
                                   OrderDate:string>>>
   )
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://mybucket/myjsondata/';
```