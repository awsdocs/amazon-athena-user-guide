# Athena engine version reference<a name="engine-versions-reference"></a>

 This section lists the changes to the Athena query engine since Athena engine version 1\. Athena engine version 1 was Athena's initial engine version and was based on [Presto 0\.172](https://prestodb.io/docs/0.172/index.html)\.

## Athena engine version 2<a name="engine-versions-reference-0002"></a>

Athena engine version 2 introduces the following changes\. Currently, Athena engine version 2 is available in all Regions where Athena is supported, including Africa \(Cape Town\), Asia Pacific \(Hong Kong\), Asia Pacific \(Mumbai\), Asia Pacific \(Osaka\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), AWS GovCloud \(US\-East\), AWS GovCloud \(US\-West\), Canada \(Central\), China \(Beijing\), China \(Ningxia\), Europe \(Frankfurt\), Europe \(Ireland\), Europe \(London\), Europe \(Milan\), Europe \(Paris\), Europe \(Stockholm\), Middle East \(Bahrain\), South America \(São Paulo\), US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), and US West \(Oregon\)\.
+  [Improvements and new features](#engine-versions-reference-0002-improvements-and-new-features) 
  +  [Grouping, join, and subquery improvements](#engine-versions-reference-0002-grouping-join-and-subquery-improvements) 
  +  [Datatype enhancements](#engine-versions-reference-0002-datatype-related-improvements) 
  +  [Added functions](#engine-versions-reference-0002-added-functions) 
  +  [Performance improvements](#engine-versions-reference-0002-improvements-performance) 
  +  [JSON\-related improvements](#engine-versions-reference-0002-improvements-json-related-improvements) 
+  [ Breaking changes](#engine-versions-reference-0002-breaking-changes) 
  +  [Bug fixes](#engine-versions-reference-0002-bug-fixes) 
  +  [Changes to geospatial functions](#engine-versions-reference-0002-changes-to-geospatial-functions) 
  +  [ANSI SQL compliance](#engine-versions-reference-0002-ansi-sql-compliance) 
  +  [Replaced functions](#engine-versions-reference-0002-replaced-functions) 
  +  [Limits](#engine-versions-reference-0002-limits) 

### Improvements and new features<a name="engine-versions-reference-0002-improvements-and-new-features"></a>
+ **EXPLAIN** and **EXPLAIN ANALYZE** – You can use the `EXPLAIN` statement in Athena to view the execution plan for your SQL queries\. Use `EXPLAIN ANALYZE` to view the distributed execution plan for your SQL queries and the cost of each operation\. For more information, see [Using EXPLAIN and EXPLAIN ANALYZE in Athena](athena-explain-statement.md)\.
+ **Federated queries** – Federated queries are supported in Athena engine version 2\. For more information, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md)\. 
+ **Geospatial functions** – More than 25 geospatial functions have been added\. For more information, see [New geospatial functions in Athena engine version 2](geospatial-functions-list-v2-function-name-changes-and-new-functions.md#geospatial-functions-list-v2-new-functions)\.
+ **Nested schema** – Support has been added for reading nested schema, which reduces cost\.
+ **Prepared statements** – Use prepared statements for repeated execution of the same query with different query parameters\. A prepared statement contains placeholder parameters whose values you pass at runtime\. Prepared statements help prevent SQL injection attacks\. For more information, see [Querying with parameterized queries](querying-with-prepared-statements.md)\.
+ **Schema evolution support** – Schema evolution support has been added for data in Parquet format\.
  + Added support for reading array, map, or row type columns from partitions where the partition schema is different from the table schema\. This can occur when the table schema was updated after the partition was created\. The changed column types must be compatible\. For row types, trailing fields may be added or dropped, but the corresponding fields \(by ordinal\) must have the same name\.
  + ORC files can now have struct columns with missing fields\. This allows the table schema to be changed without rewriting the ORC files\.
  + ORC struct columns are now mapped by name rather than ordinal\. This correctly handles missing or extra struct fields in the ORC file\.
+ **SQL OFFSET** – The SQL `OFFSET` clause is now supported in `SELECT` statements\. For more information, see [SELECT](select.md)\.
+ **UNLOAD statement** – You can use the `UNLOAD` statement to write the output of a `SELECT` query to the PARQUET, ORC, AVRO, and JSON formats\. For more information, see [UNLOAD](unload.md)\.

#### Grouping, join, and subquery improvements<a name="engine-versions-reference-0002-grouping-join-and-subquery-improvements"></a>
+ **Complex grouping** – Added support for complex grouping operations\.
+ **Correlated subqueries** – Added support for correlated subqueries in `IN` predicates and for correlated subqueries that require coercions\.
+ **CROSS JOIN** – Added support for `CROSS JOIN` against `LATERAL` derived tables\.
+ **GROUPING SETS** – Added support for `ORDER BY` clauses in aggregations for queries that use `GROUPING SETS`\.
+ **Lambda expressions** – Added support for dereferencing row fields in Lambda expressions\.
+ **Null values in semijoins** – Added support for null values on the left\-hand side of a semijoin \(that is, an `IN` predicate with subqueries\)\.
+ **Spatial joins** – Added support for broadcast spatial joins and spatial left joins\.
+ **Spill to disk** – For memory intensive `INNER JOIN` and `LEFT JOIN` operations, Athena offloads intermediate operation results to disk\. This enables execution of queries that require large amounts of memory\. 

#### Datatype enhancements<a name="engine-versions-reference-0002-datatype-related-improvements"></a>
+ **INT for INTEGER** – Added support for `INT` as an alias for the `INTEGER` data type\.
+ **INTERVAL types** – Added support for casting to `INTERVAL` types\.
+ **IPADDRESS** – Added a new `IPADDRESS` type to represent IP addresses\. Added support for casting between the `VARBINARY` type and `IPADDRESS` type\.
+ **IS DISTINCT FROM** – Added `IS DISTINCT FROM` support for the `JSON` and `IPADDRESS` types\.
+ **Null equality checks** – Equality checks for null values in `ARRAY`, `MAP`, and `ROW` data structures are now supported\. For example, the expression `ARRAY ['1', '3', null] = ARRAY ['1', '2', null]` returns `false`\. Previously, a null element returned the error message comparison not supported\.
+ **Row type coercion** – Coercion between row types regardless of field names is now allowed\. Previously, a row type was coercible to another only if the field name in the source type matched the target type, or when the target type had an anonymous field name\.
+ **Time subtraction** – Implemented subtraction for all `TIME` and `TIMESTAMP` types\.
+ **Unicode** – Added support for escaped Unicode sequences in string literals\.
+ **VARBINARY concatenation** – Added support for concatenation of `VARBINARY` values\.

  **Window value functions** – Window value functions now support `IGNORE NULLS` and `RESPECT NULLS`\.

##### Additional input types for functions<a name="engine-versions-reference-0002-additional-input-datatypes-for-functions"></a>

The following functions now accept additional input types\. For more information about each function, visit the corresponding link to the Presto documentation\.
+ **approx\_distinct\(\)** – The [approx\_distinct\(\)](https://prestodb.io/docs/0.217/functions/aggregate.html#approx_distinct) function now supports the following types: `INTEGER`, `SMALLINT`, `TINYINT`, `DECIMAL`, `REAL`, `DATE`, `TIMESTAMP`, `TIMESTAMP WITH TIME ZONE`, `TIME`, `TIME WITH TIME ZONE`, `IPADDRESS`, and `CHAR`\.
+ **Avg\(\), sum\(\)** – The [avg\(\)](https://prestodb.io/docs/0.217/functions/aggregate.html#avg) and [sum\(\)](https://prestodb.io/docs/0.217/functions/aggregate.html#sum) aggregate functions now support the `INTERVAL` data type\.
+ **Lpad\(\), rpad\(\)** – The [lpad](https://prestodb.io/docs/0.217/functions/string.html#lpad) and [rpad](https://prestodb.io/docs/0.217/functions/string.html#rpad) functions now work on `VARBINARY` inputs\.
+ **Min\(\), max\(\)** – The [min\(\)](https://prestodb.io/docs/0.217/functions/aggregate.html#min) and [max\(\)](https://prestodb.io/docs/0.217/functions/aggregate.html#max) aggregation functions now allow unknown input types at query analysis time so that you can use the functions with `NULL` literals\.
+ **regexp\_replace\(\)** – Variant of the [regexp\_replace\(\)](https://prestodb.io/docs/0.217/functions/regexp.html#regexp_replace) function added that can execute a Lambda function for each replacement\.
+ **Sequence\(\)** – Added `DATE` variants for the [sequence\(\)](https://prestodb.io/docs/0.217/functions/array.html#sequence) function, including variant with an implicit one\-day step increment\.
+ **ST\_Area\(\)** – The [ST\_Area\(\)](https://prestodb.io/docs/0.217/functions/geospatial.html#ST_Area) geospatial function now supports all geometry types\. 
+ **Substr\(\)** – The [substr](https://prestodb.io/docs/0.217/functions/string.html#substr) function now works on `VARBINARY` inputs\.
+ **zip\_with\(\)** – Arrays of mismatched length can now be used with [zip\_with\(\)](https://prestodb.io/docs/0.217/functions/array.html#zip_with)\. Missing positions are filled with null\. Previously, an error was raised when arrays of differing lengths were passed\. This change may make it difficult to distinguish between values that were originally null from values that were added to pad the arrays to the same length\.

#### Added functions<a name="engine-versions-reference-0002-added-functions"></a>

The following list contains functions that are new in Athena engine version 2\. The list does not include geospatial functions\. For a list of geospatial functions, see [New geospatial functions in Athena engine version 2](geospatial-functions-list-v2-function-name-changes-and-new-functions.md#geospatial-functions-list-v2-new-functions)\.

For more information about each function, visit the corresponding link to the Presto documentation\.

##### Aggregate functions<a name="engine-versions-reference-0002-aggregate-functions"></a>

 [reduce\_agg\(\)](https://prestodb.io/docs/0.217/functions/aggregate.html#reduce_agg) 

##### Array functions and operators<a name="engine-versions-reference-0002-array-functions-and-operators"></a>

[array\_sort\(\)](https://prestodb.io/docs/0.217/functions/array.html#array_sort) \- Variant of this function added that takes a Lambda function as a comparator\.

 [ngrams\(\)](https://prestodb.io/docs/0.217/functions/array.html#ngrams) 

##### Binary functions and operators<a name="engine-versions-reference-0002-binary-functions-and-operators"></a>

 [from\_big\_endian\_32\(\)](https://prestodb.io/docs/0.217/functions/binary.html#from_big_endian_32) 

 [from\_ieee754\_32\(\)](https://prestodb.io/docs/0.217/functions/binary.html#from_ieee754_32) 

 [from\_ieee754\_64\(\)](https://prestodb.io/docs/0.217/functions/binary.html#from_ieee754_64) 

 [hmac\_md5\(\)](https://prestodb.io/docs/0.217/functions/binary.html#hmac_md5) 

 [hmac\_sha1\(\)](https://prestodb.io/docs/0.217/functions/binary.html#hmac_sha1) 

 [hmac\_sha256\(\)](https://prestodb.io/docs/0.217/functions/binary.html#hmac_sha256) 

 [hmac\_sha512\(\)](https://prestodb.io/docs/0.217/functions/binary.html#hmac_sha512) 

 [spooky\_hash\_v2\_32\(\)](https://prestodb.io/docs/0.217/functions/binary.html#spooky_hash_v2_32) 

 [spooky\_hash\_v2\_64\(\)](https://prestodb.io/docs/0.217/functions/binary.html#spooky_hash_v2_64) 

 [to\_big\_endian\_32\(\)](https://prestodb.io/docs/0.217/functions/binary.html#to_big_endian_32) 

 [to\_ieee754\_32\(\)](https://prestodb.io/docs/0.217/functions/binary.html#to_ieee754_32) 

 [to\_ieee754\_64\(\)](https://prestodb.io/docs/0.217/functions/binary.html#to_ieee754_64) 

##### Date and time functions and operators<a name="engine-versions-reference-0002-date-and-time-functions-and-operators"></a>

 [millisecond\(\)](https://prestodb.io/docs/0.217/functions/datetime.html#millisecond) 

 [parse\_duration\(\)](https://prestodb.io/docs/0.217/functions/datetime.html#parse_duration) 

 [to\_milliseconds\(\)](https://prestodb.io/docs/0.217/functions/datetime.html#to_milliseconds) 

##### Map functions and operators<a name="engine-versions-reference-0002-map-functions-and-operators"></a>

 [multimap\_from\_entries\(\)](https://prestodb.io/docs/0.217/functions/map.html#multimap_from_entries) 

##### Mathematical functions and operators<a name="engine-versions-reference-0002-mathematical-functions-and-operators"></a>

 [inverse\_normal\_cdf\(\)](https://prestodb.io/docs/0.217/functions/math.html#inverse_normal_cdf) 

 [wilson\_interval\_lower\(\)](https://prestodb.io/docs/0.217/functions/math.html#wilson_interval_lower) 

 [wilson\_interval\_upper\(\)](https://prestodb.io/docs/0.217/functions/math.html#wilson_interval_upper) 

##### Quantile digest functions<a name="engine-versions-reference-0002-quantile-digest-functions"></a>

[quantile digest functions](https://prestodb.io/docs/0.217/functions/qdigest.html) and the `qdigest` quantile digest type added\.

##### String functions and operators<a name="engine-versions-reference-0002-string-functions-and-operators"></a>

 [hamming\_distance\(\)](https://prestodb.io/docs/0.217/functions/string.html#hamming_distance) 

 [split\_to\_multimap\(\)](https://prestodb.io/docs/0.217/functions/string.html#split_to_multimap) 

#### Performance improvements<a name="engine-versions-reference-0002-improvements-performance"></a>

Performance of the following features has improved in Athena engine version 2\.

##### Query performance<a name="engine-versions-reference-0002-improvements-performance-query"></a>
+ **Broadcast join performance** – Improved broadcast join performance by applying dynamic partition pruning in the worker node\. 
+ **Bucketed tables** – Improved performance for writing to bucketed tables when the data being written is already partitioned appropriately \(for example, when the output is from a bucketed join\)\.
+ **DISTINCT** – Improved performance for some queries that use `DISTINCT`\.

  **Dynamic filtering and partition pruning** – Improvements increase performance and reduce the amount of data scanned in queries\. 
+ **Filter and projection operations** – Filter and projection operations are now always processed by columns if possible\. The engine automatically takes advantage of dictionary encodings where effective\.
+ **Gathering exchanges** – Improved performance for queries with gathering exchanges\.
+ **Global aggregations** – Improved performance for some queries that perform filtered global aggregations\.
+ **GROUPING SETS, CUBE, ROLLUP** – Improved performance for queries involving `GROUPING SETS`, `CUBE` or `ROLLUP`, which you can use to aggregate multiple sets of columns in a single query\.
+ **Highly selective filters** – Improved the performance of queries with highly selective filters\.
+ **JOIN and AGGREGATE operations** – The performance of `JOIN` and `AGGREGATE` operations has been enhanced\.
+ **LIKE** – Improved the performance of queries that use `LIKE` predicates on the columns of `information_schema` tables\.
+ **ORDER BY and LIMIT** – Improved plans, performance, and memory usage for queries involving `ORDER BY` and `LIMIT` to avoid unnecessary data exchanges\.
+ **ORDER BY** – `ORDER BY` operations are now distributed by default, enabling larger `ORDER BY` clauses to be used\.
+ **ROW type conversions** – Improved performance when converting between `ROW` types\.
+ **Structural types** – Improved performance of queries that process structural types and contain scan, joins, aggregations, or table writes\.
+ **Table scans** – An optimization rule has been introducted to avoid duplicate table scans in certain cases\.
+ **UNION** – Improved performance for `UNION` queries\.

##### Query planning performance<a name="engine-versions-reference-0002-improvements-performance-query-planning"></a>
+ **Planning performance** – Improved planning performance for queries that join multiple tables with a large number of columns\.
+ **Predicate evaluations** – Improved predicate evaluation performance during predicate pushdown in planning\.
+ **Predicate pushdown support for casting** – Support predicate pushdown for the *<column>* `IN` *<values list>* predicate where values in the values list require casting to match the type of column\.
+ **Predicate inference and pushdown** – Predicate inference and pushdown extended for queries that use a *<symbol>* `IN` *<subquery>* predicate\.
+ **Timeouts** – Fixed a bug that could in rare cases cause query planning timeouts\.

##### Join performance<a name="engine-versions-reference-0002-improvements-performance-join"></a>
+ **Joins with map columns** – Improved the performance of joins and aggregations that include map columns\.
+ **Joins with solely non\-equality conditions** – Improved the performance of joins with only non\-equality conditions by using a nested loop join instead of a hash join\.
+ **Outer joins** – The join distribution type is now automatically selected for queries involving outer joins\.
+ **Range over a function joins** – Improved performance of joins where the condition is a range over a function \(for example, `a JOIN b ON b.x < f(a.x) AND b.x > g(a.x)`\)\.
+ **Spill\-to\-disk** – Fixed spill\-to\-disk related bugs and memory issues to enhance performance and reduce memory errors in `JOIN` operations\.

##### Subquery performance<a name="engine-versions-reference-0002-improvements-performance-subquery"></a>
+ **Correlated EXISTS subqueries** – Improved performance of correlated `EXISTS` subqueries\.
+ **Correlated subqueries with equalities** – Improved support for correlated subqueries containing equality predicates\.
+ **Correlated subqueries with inequalities** – Improved performance for correlated subqueries that contain inequalities\.
+ **Count\(\*\) aggregations over subqueries** – Improved performance of `count(*)` aggregations over subqueries with known constant cardinality\.
+ **Outer query filter propagation** – Improved performance of correlated subqueries when filters from the outer query can be propagated to the subquery\.

##### Function performance<a name="engine-versions-reference-0002-improvements-performance-functions"></a>
+ **Aggregate window functions** – Improved performance of aggregate window functions\.
+ **element\_at\(\)** – Improved performance of `element_at()` for maps to be constant time rather than proportional to the size of the map\.
+ **Grouping\(\)** – Improved performance for queries involving `grouping()`\.
+ **JSON casting** – Improved the performance of casting from `JSON` to `ARRAY` or `MAP` types\.
+ **Map\-returning functions** – Improved performance of functions that return maps\.
+ **Map\-to\-map casting** – Improved the performance of map\-to\-map cast\.
+ **Min\(\) and max\(\)** – The `min()` and `max()` functions have been optimized to avoid unnecessary object creation, thus reducing garbage collection overhead\.
+ **row\_number\(\)** – Improved performance and memory usage for queries using `row_number()` followed by a filter on the row numbers generated\.
+ **Window functions** – Improved performance of queries containing window functions with identical `PARTITION BY` and `ORDER BY` clauses\.
+ **Window functions** – Improved performance of certain window functions \(for example, `LAG`\) that have similar specifications\.

##### Geospatial performance<a name="engine-versions-reference-0002-improvements-performance-geospatial"></a>
+ **Geometry serialization** – Improved the serialization performance of geometry values\.
+ **Geospatial functions** – Improved the performance of `ST_Intersects()`, `ST_Contains()`, `ST_Touches()`, `ST_Within()`, `ST_Overlaps()`, `ST_Disjoint()`, `transform_values()`, `ST_XMin()`, `ST_XMax()`, `ST_YMin()`, `ST_YMax()`, `ST_Crosses()`, and `array_intersect()`\.
+ **ST\_Distance\(\)** – Improved performance of join queries involving the `ST_Distance()` function\.
+ **ST\_Intersection\(\)** – Optimized the `ST_Intersection()` function for rectangles aligned with coordinate axes \(for example, polygons produced by the `ST_Envelope()` and `bing_tile_polygon()` functions\)\.

#### JSON\-related improvements<a name="engine-versions-reference-0002-improvements-json-related-improvements"></a>

 **Map Functions** 
+ Improved performance of map subscript from `O(n)` to `O(1)` in all cases\. Previously, only maps produced by certain functions and readers took advantage of this improvement\.
+ Added the `map_from_entries()` and `map_entries()` functions\.

 **Casting** 
+ Added ability to cast to `JSON` from `REAL`, `TINYINT` or `SMALLINT`\.
+ You can now cast `JSON` to `ROW` even if the `JSON` does not contain every field in the `ROW`\.
+ Improved performance of `CAST(json_parse(...) AS ...)`\.
+ Improved the performance of casting from `JSON` to `ARRAY` or `MAP` types\.

 **New JSON Functions** 
+  [is\_json\_scalar\(\)](https://prestodb.io/docs/0.217/functions/json.html#is_json_scalar) 

### Breaking changes<a name="engine-versions-reference-0002-breaking-changes"></a>

Breaking changes include bug fixes, changes to geospatial functions, replaced functions, and the introduction of limits\. Improvements in ANSI SQL compliance may break queries that depended on non\-standard behavior\.

#### Bug fixes<a name="engine-versions-reference-0002-bug-fixes"></a>

The following changes correct behavioral issues that caused queries to run successfully, but with inaccurate results\.
+ **fixed\_len\_byte\_array parquet columns are now accepted as DECIMAL** – Queries on Parquet columns of type `fixed_len_byte_array` succeed and return correct values if they are annotated as `DECIMAL` in the Parquet Schema\. Queries on `fixed_len_byte_array` columns without the `DECIMAL` annotation fail with an error\. Previously, queries on `fixed_len_byte_array` columns without the DECIMAL annotation succeeded but returned incomprehensible values\. 
+ **json\_parse\(\) no longer ignores trailing characters** – Previously, inputs such as `[1,2]abc` would successfully parse as `[1,2]`\. Using trailing characters now produces the error message Cannot convert '\[1, 2\]abc' to JSON\.
+ **Round\(\) decimal precision corrected** – `round(x, d)` now correctly rounds `x` when `x` is a DECIMAL or when `x` is a DECIMAL with scale 0 and `d` is a negative integer\. Previously, no rounding occurred in these cases\.
+ **round\(x, d\) and truncate\(x, d\)** – The parameter `d` in the signature of functions `round(x, d)` and `truncate(x, d)` is now of type `INTEGER`\. Previously, `d` could be of type `BIGINT`\.
+ **Map\(\) with duplicate keys** – `map()` now raises an error on duplicate keys rather than silently producing a corrupted map\. Queries that currently construct map values using duplicate keys now fail with an error\.
+ **map\_from\_entries\(\) raises an error with null entries ** – `map_from_entries()` now raises an error when the input array contains a null entry\. Queries that construct a map by passing `NULL` as a value now fail\.
+ **Tables** – Tables that have unsupported partition types can no longer be created\.
+ **Improved numerical stability in statistical functions ** – The numerical stability for the statistical functions `corr()`, `covar_samp()`, `regr_intercept()`, and `regr_slope()` has been improved\.
+ **TIMESTAMP precision defined in parquet is now respected** – The precision of `TIMESTAMP` values and the precision defined for the `TIMESTAMP` column in the Parquet schema must now match\. Non\-matching precisions result in incorrect timestamps\. 
+ **Time zone information** – Time zone information is now calculated using the [java\.time](https://docs.oracle.com/javase/8/docs/api/java/time/package-summary.html) package of the Java 1\.8 SDK\.
+ **SUM of INTERVAL\_DAY\_TO\_SECOND and INTERVAL\_YEAR\_TO\_MONTH datatypes ** – You can no longer use `SUM(NULL)` directly\. In order to use `SUM(NULL)`, cast `NULL` to a data type like `BIGINT`, `DECIMAL`, `REAL`, `DOUBLE`, `INTERVAL_DAY_TO_SECOND` or `INTERVAL_YEAR_TO_MONTH`\.

#### Changes to geospatial functions<a name="engine-versions-reference-0002-changes-to-geospatial-functions"></a>

Changes made to geospatial functions include the following\.
+ **Function name changes** – Some function names have changed\. For more information, see [Geospatial function name changes in Athena engine version 2](geospatial-functions-list-v2-function-name-changes-and-new-functions.md#geospatial-functions-list-v2-function-name-changes)\. 
+ **VARBINARY input** – The `VARBINARY` type is no longer directly supported for input to geospatial functions\. For example, to calculate the area of a geometry directly, the geometry must now be input in either `VARCHAR` or `GEOMETRY` format\. The workaround is to use transform functions, as in the following examples\.
  + To use `ST_area()` to calculate the area for `VARBINARY` input in Well\-Known Binary \(WKB\) format, pass the input to `ST_GeomFromBinary()` first, for example:

    ```
    ST_area(ST_GeomFromBinary(<wkb_varbinary_value>))
    ```
  + To use `ST_area()` to calculate the area for `VARBINARY` input in legacy binary format, pass the same input to the `ST_GeomFromLegacyBinary()` function first, for example:

    ```
    ST_area(ST_GeomFromLegacyBinary(<legacy_varbinary_value>))
    ```
+ **ST\_ExteriorRing\(\) and ST\_Polygon\(\)** – [`ST_ExteriorRing()`](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-exteriorring) and [`ST_Polygon()`](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-polygon) now accept only polygons as inputs\. Previously, these functions erroneously accepted other geometries\.
+ **ST\_Distance\(\)** – As required by the [SQL/MM specification](http://doesen0.informatik.uni-leipzig.de/proceedings/paper/68.pdf), the [`ST_Distance()`](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-distance) function now returns `NULL` if one of the inputs is an empty geometry\. Previously, `NaN` was returned\.

#### ANSI SQL compliance<a name="engine-versions-reference-0002-ansi-sql-compliance"></a>

The following syntax and behavioral issues have been corrected to follow the ANSI SQL standard\.
+ **Cast\(\) operations** – Cast\(\) operations from REAL or DOUBLE to DECIMAL now conform to the SQL standard\. For example, `cast (double '100000000000000000000000000000000' as decimal(38))` previously returned `100000000000000005366162204393472` but now returns `100000000000000000000000000000000`\.
+ **JOIN \.\.\. USING** – `JOIN ... USING` now conforms to standard SQL semantics\. Previously, `JOIN ... USING` required qualifying the table name in columns, and the column from both tables would be present in the output\. Table qualifications are now invalid and the column is present only once in the output\.
+ **ROW type literals removed** – The ROW type literal format `ROW<int, int>(1, 2)` is no longer supported\. Use the syntax `ROW(1 int, 2 int)` instead\.
+ **Log\(\) function** – Previously, in violation of the SQL standard, the order of the arguments in the `log()` function was reversed\. This caused `log()` to return incorrect results when queries were translated to or from other SQL implementations\. The equivalent to `log(x, b)` is now correctly `ln(x) / ln(b)`\.
+ **Grouped aggregation semantics** – Grouped aggregations use `IS NOT DISTINCT FROM` semantics rather than equality semantics\. Grouped aggregations now return correct results and show improved performance when grouping on `NaN` floating point values\. Grouping on map, list, and row types that contain nulls is supported\.
+ **Types with quotation marks are no longer allowed** – In accordance with the ANSI SQL standard, data types can no longer be enclosed in quotation marks\. For example, `SELECT "date" '2020-02-02'` is no longer a valid query\. Instead, use the syntax `SELECT date '2020-02-02'`\.
+ **Anonymous row field access** – Anonymous row fields can no longer be accessed by using the syntax \[`.field0, .field1, ...`\]\.
+ **Complex grouping operations** – The complex grouping operations `GROUPING SETS`, `CUBE`, and `ROLLUP` do not support grouping on expressions composed of input columns\. Only column names are allowed\.

#### Replaced functions<a name="engine-versions-reference-0002-replaced-functions"></a>

The following functions are no longer supported and have been replaced by syntax that produces the same results\.
+ **information\_schema\.\_\_internal\_partitions\_\_** – The usage of `__internal_partitions__` is no longer supported in Athena engine version 2\. For equivalent syntax, use `SELECT * FROM "<table_name>$partitions"` or `SHOW PARTITIONS`\. For more information, see [Listing partitions for a specific table](querying-glue-catalog.md#querying-glue-catalog-listing-partitions)\.
+ **Replaced geospatial functions** – For a list of geospatial functions whose names have changed, see [Geospatial function name changes in Athena engine version 2](geospatial-functions-list-v2-function-name-changes-and-new-functions.md#geospatial-functions-list-v2-function-name-changes)\.

#### Limits<a name="engine-versions-reference-0002-limits"></a>

The following limits were introduced in Athena engine version 2 to ensure that queries do not fail due to resource limitations\. These limits are not configurable by users\.
+ **Number of result elements** – The number of result elements `n` is restricted to 10,000 or less for the following functions: `min(col, n)`, `max(col, n)`, `min_by(col1, col2, n)`, and `max_by(col1, col2, n)`\.
+ **GROUPING SETS** – The maximum number of slices in a grouping set is 2048\.
+ **Maximum text file line length** – The default maximum line length for text files is 100 MB\.
+ **Sequence function maximum result size** – The maximum result size of a sequence function is 50000 entries\. For example, `SELECT sequence(0,45000,1)` succeeds, but `SELECT sequence(0,55000,1)` fails with the error message The result of the sequence function must not have more than 50000 entries\. This limit applies to all input types for sequence functions, including timestamps\.