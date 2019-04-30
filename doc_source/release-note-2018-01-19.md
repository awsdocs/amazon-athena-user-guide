# January 19, 2018<a name="release-note-2018-01-19"></a>

Published on *2018\-01\-19*

Athena uses Presto, an open\-source distributed query engine, to run queries\. 

With Athena, there are no versions to manage\. We have transparently upgraded the underlying engine in Athena to a version based on Presto version 0\.172\. No action is required on your end\. 

With the upgrade, you can now use [Presto 0\.172 Functions and Operators](https://prestosql.io/docs/0.172/functions.html), including [Presto 0\.172 Lambda Expressions](https://prestosql.io/docs/0.172/functions/lambda.html) in Athena\. 

Major updates for this release, including the community\-contributed fixes, include:
+ Support for ignoring headers\. You can use the `skip.header.line.count` property when defining tables, to allow Athena to ignore headers\. This is currently supported for queries that use the OpenCSV SerDe, and not for Grok or Regex SerDes\.
+ Support for the `CHAR(n)` data type in `STRING` functions\. The range for `CHAR(n)` is `[1.255]`, while the range for `VARCHAR(n)` is `[1,65535]`\. 
+ Support for correlated subqueries\.
+ Support for Presto Lambda expressions and functions\.
+ Improved performance of the `DECIMAL` type and operators\.
+ Support for filtered aggregations, such as `SELECT sum(col_name) FILTER`, where `id > 0`\.
+ Push\-down predicates for the `DECIMAL`, `TINYINT`, `SMALLINT`, and `REAL` data types\.
+ Support for quantified comparison predicates: `ALL`, `ANY`, and `SOME`\. 
+ Added functions: [https://prestosql.io/docs/0.172/functions/array.html#arrays_overlap](https://prestosql.io/docs/0.172/functions/array.html#arrays_overlap), [https://prestosql.io/docs/0.172/functions/array.html#array_except](https://prestosql.io/docs/0.172/functions/array.html#array_except), [https://prestosql.io/docs/0.172/functions/string.html#levenshtein_distance](https://prestosql.io/docs/0.172/functions/string.html#levenshtein_distance), [https://prestosql.io/docs/0.172/functions/string.html#codepoint](https://prestosql.io/docs/0.172/functions/string.html#codepoint), [https://prestosql.io/docs/0.172/functions/aggregate.html#skewness](https://prestosql.io/docs/0.172/functions/aggregate.html#skewness), [https://prestosql.io/docs/0.172/functions/aggregate.html#kurtosis](https://prestosql.io/docs/0.172/functions/aggregate.html#kurtosis), and [https://prestosql.io/docs/0.172/functions/conversion.html#typeof](https://prestosql.io/docs/0.172/functions/conversion.html#typeof)\.
+ Added a variant of the [https://prestosql.io/docs/0.172/functions/datetime.html#from_unixtime](https://prestosql.io/docs/0.172/functions/datetime.html#from_unixtime) function that takes a timezone argument\.
+ Added the [https://prestosql.io/docs/0.172/functions/aggregate.html#bitwise_and_agg](https://prestosql.io/docs/0.172/functions/aggregate.html#bitwise_and_agg) and [https://prestosql.io/docs/0.172/functions/aggregate.html#bitwise_or_agg](https://prestosql.io/docs/0.172/functions/aggregate.html#bitwise_or_agg) aggregation functions\.
+  Added the [https://prestosql.io/docs/0.172/functions/binary.html#xxhash64](https://prestosql.io/docs/0.172/functions/binary.html#xxhash64) and [https://prestosql.io/docs/0.172/functions/binary.html#to_big_endian_64](https://prestosql.io/docs/0.172/functions/binary.html#to_big_endian_64) functions\. 
+ Added support for escaping double quotes or backslashes using a backslash with a JSON path subscript to the [https://prestosql.io/docs/0.172/functions/json.html#json_extract](https://prestosql.io/docs/0.172/functions/json.html#json_extract) and [https://prestosql.io/docs/0.172/functions/json.html#json_extract_scalar](https://prestosql.io/docs/0.172/functions/json.html#json_extract_scalar) functions\. This changes the semantics of any invocation using a backslash, as backslashes were previously treated as normal characters\.

For a complete list of functions and operators, see [SQL Queries, Functions, and Operators](functions-operators-reference-section.md) in this guide, and [Presto 0\.172 Functions]( https://prestosql.io/docs/0.172/functions.html)\. 

Athena does not support all of Presto's features\. For more information, see [Limitations](other-notable-limitations.md)\.