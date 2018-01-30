# Input Data Formats and Geometry Data Types<a name="geospatial-input-data-formats-supported-geometry-types"></a>

To use geospatial functions in Athena, input your data in the WKT or WKB formats, or use the Hive JSON SerDe\. You can also use the geometry data types supported in Athena\.

## Input Data Formats<a name="input-data-formats"></a>

To handle geospatial queries, Athena supports input data in these data formats:

+  **WKT \(Well\-known Text\)**\. In Athena, WKT is represented as a `varchar` data type\.

+  **WKB \(Well\-known binary\)**\. In Athena, WKB is represented as a `varbinary` data type with a spatial reference ID \(WKID\) 4326\. For information about both of these types, see the Wikipedia topic on [Well\-known text](https://en.wikipedia.org/wiki/Well-known_text)\.

+  **JSON\-encoded geospatial data**\. To parse JSON files with geospatial data and create tables for them, Athena uses the [Hive JSON SerDe](https://github.com/Esri/spatial-framework-for-hadoop/wiki/Hive-JSON-SerDe)\. For more information about using this SerDe in Athena, see JSON SerDe Libraries\.

## Geometry Data Types<a name="geometry-data-types"></a>

To handle geospatial queries, Athena supports these specialized geometry data types:

+  `point` 

+  `line` 

+  `polygon` 

+  `multiline` 

+  `multipolygon` 