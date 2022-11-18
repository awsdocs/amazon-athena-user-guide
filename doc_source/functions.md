# Functions in Amazon Athena<a name="functions"></a>

Athena supports some, but not all, Trino and Presto functions\. For information, see [Considerations and limitations](other-notable-limitations.md)\. For a list of the time zones that can be used with the `AT TIME ZONE` operator, see [Supported time zones](athena-supported-time-zones.md)\.

## Athena engine version 3<a name="functions-env3"></a>

Functions in Athena engine version 3 are based on Trino\. For information about Trino functions, operators, and expressions, see [Functions and operators](https://trino.io/docs/current/functions.html) and the following subsections from the Trino documentation\.
+  [Aggregate](https://trino.io/docs/current/functions/aggregate.html) 
+  [Array](https://trino.io/docs/current/functions/array.html) 
+  [Binary](https://trino.io/docs/current/functions/binary.html) 
+  [Bitwise](https://trino.io/docs/current/functions/bitwise.html) 
+  [Color](https://trino.io/docs/current/functions/color.html) 
+  [Comparison](https://trino.io/docs/current/functions/comparison.html) 
+  [Conditional](https://trino.io/docs/current/functions/conditional.html) 
+  [Conversion](https://trino.io/docs/current/functions/conversion.html) 
+  [Date and time](https://trino.io/docs/current/functions/datetime.html) 
+  [Decimal](https://trino.io/docs/current/functions/decimal.html) 
+  [Geospatial](https://trino.io/docs/current/functions/geospatial.html) 
+  [HyperLogLog](https://trino.io/docs/current/functions/hyperloglog.html) 
+  [IP Address](https://trino.io/docs/current/functions/ipaddress.html) 
+  [JSON](https://trino.io/docs/current/functions/json.html) 
+  [Lambda](https://trino.io/docs/current/functions/lambda.html) 
+  [Logical](https://trino.io/docs/current/functions/logical.html) 
+  [Machine learning](https://trino.io/docs/current/functions/ml.html) 
+  [Map](https://trino.io/docs/current/functions/map.html) 
+  [Math](https://trino.io/docs/current/functions/math.html) 
+  [Quantile digest](https://trino.io/docs/current/functions/qdigest.html) 
+  [Regular expression](https://trino.io/docs/current/functions/regexp.html) 
+  [Session](https://trino.io/docs/current/functions/session.html) 
+  [Set Digest](https://trino.io/docs/current/functions/setdigest.html) 
+  [String](https://trino.io/docs/current/functions/string.html) 
+  [System](https://trino.io/docs/current/functions/system.html) 
+  [Table](https://trino.io/docs/current/functions/table.html) 
+  [Teradata](https://trino.io/docs/current/functions/teradata.html) 
+  [T\-Digest](https://trino.io/docs/current/functions/tdigest.html) 
+  [URL](https://trino.io/docs/current/functions/url.html) 
+  [UUID](https://trino.io/docs/current/functions/uuid.html) 
+  [Window](https://trino.io/docs/current/functions/window.html) 

## Athena engine version 2<a name="functions-env2"></a>

Functions in Athena engine version 2 are based on [Presto 0\.217](https://prestodb.io/docs/current/release/release-0.217.html)\. For the geospatial functions in Athena engine version 2, see [Geospatial functions in Athena engine version 2](geospatial-functions-list-v2.md)\.

**Note**  
Version\-specific documentation for Presto 0\.217 functions is no longer available\. For information about current Presto functions, operators, and expressions, see [Presto functions and operators](https://prestodb.io/docs/current/functions.html), or visit the subcategory links in this section\.
+ [Logical operators](https://prestodb.io/docs/current/functions/logical.html)
+ [Comparison functions and operators](https://prestodb.io/docs/current/functions/comparison.html)
+ [Conditional expressions](https://prestodb.io/docs/current/functions/conditional.html)
+ [Conversion functions](https://prestodb.io/docs/current/functions/conversion.html)
+ [Mathematical functions and operators](https://prestodb.io/docs/current/functions/math.html)
+ [Bitwise functions](https://prestodb.io/docs/current/functions/bitwise.html)
+ [Decimal functions and operators](https://prestodb.io/docs/current/functions/decimal.html)
+ [String functions and operators](https://prestodb.io/docs/current/functions/string.html)
+ [Binary functions](https://prestodb.io/docs/current/functions/binary.html)
+ [Date and time functions and operators](https://prestodb.io/docs/current/functions/datetime.html)
+ [Regular expression functions](https://prestodb.io/docs/current/functions/regexp.html)
+ [JSON functions and operators](https://prestodb.io/docs/current/functions/json.html)
+ [URL functions](https://prestodb.io/docs/current/functions/url.html)
+ [Aggregate functions](https://prestodb.io/docs/current/functions/aggregate.html)
+ [Window functions](https://prestodb.io/docs/current/functions/window.html)
+ [Color functions](https://prestodb.io/docs/current/functions/color.html)
+ [Array functions and operators](https://prestodb.io/docs/current/functions/array.html)
+ [Map functions and operators](https://prestodb.io/docs/current/functions/map.html)
+ [Lambda expressions and functions](https://prestodb.io/docs/current/functions/lambda.html)
+ [Teradata functions](https://prestodb.io/docs/current/functions/teradata.html)