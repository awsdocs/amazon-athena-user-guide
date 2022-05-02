# Input data formats and geometry data types<a name="geospatial-input-data-formats-supported-geometry-types"></a>

To use geospatial functions in Athena, input your data in the WKT format, or use the Hive JSON SerDe\. You can also use the geometry data types supported in Athena\.

## Input data formats<a name="input-data-formats"></a>

To handle geospatial queries, Athena supports input data in these data formats:
+  **WKT \(Well\-known Text\)**\. In Athena, WKT is represented as a `varchar` data type\.
+  **JSON\-encoded geospatial data**\. To parse JSON files with geospatial data and create tables for them, Athena uses the [Hive JSON SerDe](https://github.com/Esri/spatial-framework-for-hadoop/wiki/Hive-JSON-SerDe)\. For more information about using this SerDe in Athena, see [JSON SerDe libraries](json-serde.md)\.

## Geometry data types<a name="geometry-data-types"></a>

To handle geospatial queries, Athena supports these specialized geometry data types:
+  `point` 
+  `line` 
+  `polygon` 
+  `multiline` 
+  `multipolygon` 