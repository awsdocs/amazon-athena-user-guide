# List of Supported Geospatial Functions<a name="geospatial-functions-list"></a>

Geospatial functions in Athena have these characteristics:
+ The functions follow the general principles of [Spatial Query](https://en.wikipedia.org/wiki/Spatial_query)\.
+ The functions are implemented as a Presto plugin that uses the ESRI Java Geometry Library\. This library has an Apache 2 license\.
+ The functions rely on the [ESRI Geometry API](https://esri.github.io/geometry-api-java/javadoc/index.html?overview-summary.html)\.
+ Not all of the ESRI\-supported functions are available in Athena\. This topic lists only the ESRI geospatial functions that are supported in Athena\.
+ You cannot use views with geospatial functions\.

Athena supports four types of geospatial functions:
+  [Constructor Functions](#constructors-functions) 
+  [Geospatial Relationship Functions](#geospatial-relationships-functions) 
+  [Operation Functions](#operations-functions) 
+  [Accessor Functions](#accessors-functions) 

## Before You Begin<a name="before-you-begin"></a>

Create two tables, `earthquakes` and `counties`, as follows:

```
CREATE external TABLE earthquakes
(
 earthquake_date STRING,
 latitude DOUBLE,
 longitude DOUBLE,
 depth DOUBLE,
 magnitude DOUBLE,
 magtype string,
 mbstations string,
 gap string,
 distance string,
 rms string,
 source string,
 eventid string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE LOCATION 's3://my-query-log/csv'
```

```
CREATE external TABLE IF NOT EXISTS counties
 (
 Name string,
 BoundaryShape binary
 )
ROW FORMAT SERDE 'com.esri.hadoop.hive.serde.JsonSerde'
STORED AS INPUTFORMAT 'com.esri.json.hadoop.EnclosedJsonInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION 's3://my-query-log/json'
```

Some of the subsequent examples are based on these tables and rely on two sample files stored in the Amazon S3 location\. These files are not included with Athena and are used for illustration purposes only:
+ An `earthquakes.csv` file, which lists earthquakes that occurred in California\. This file has fields that correspond to the fields in the table `earthquakes`\.
+ A `california-counties.json` file, which lists JSON\-encoded county data in the ESRI\-compliant format, and includes many fields, such as `AREA`, `PERIMETER`, `STATE`, `COUNTY`, and `NAME`\. The `counties` table is based on this file and has two fields only: `Name` \(string\), and `BoundaryShape` \(binary\)\.

## Constructor Functions<a name="constructors-functions"></a>

Use constructor functions to obtain binary representations of `point`, `line`, or `polygon` geometry data types\. You can also use these functions to convert binary data to text, and obtain binary values for geometry data that is expressed as Well\-Known Text \(WKT\)\. 

### `ST_POINT(double, double)`<a name="st-point-double-double"></a>

Returns a binary representation of a `point` geometry data type\.

To obtain the `point` geometry data type, use the ST\_POINT function in Athena\. For the input data values to this function, use geometric values, such as values in the Universal Transverse Mercator \(UTM\) Cartesian coordinate system, or geographic map units \(longitude and latitude\) in decimal degrees\. The longitude and latitude values use the World Geodetic System, also known as WGS 1984, or EPSG:4326\. WGS 1984 is the coordinate system used by the Global Positioning System \(GPS\)\. 

For example, in the following notation, the map coordinates are specified in longitude and latitude, and the value `.072284`, which is the buffer distance, is specified in angular units as decimal degrees:

```
ST_BUFFER(ST_POINT(-74.006801, 40.705220), .072284)
```

Syntax:

```
SELECT ST_POINT(longitude, latitude) FROM earthquakes LIMIT 1;
```

In the alternative syntax, you can also specify the coordinates as a `point` data type with two values:

```
SELECT ST_POINT('point (-74.006801 40.705220)');
```

Example\. This example uses specific longitude and latitude coordinates from `earthquakes.csv`:

```
SELECT ST_POINT(61.56, -158.54)
FROM earthquakes
LIMIT 1;
```

It returns this binary representation of a geometry data type `point`:

```
00 00 00 00 01 01 00 00 00 48 e1 7a 14 ae c7 4e 40 e1 7a 14 ae 47 d1 63 c0
```

The next example uses specific longitude and latitude coordinates: 

```
SELECT ST_POINT(-74.006801, 40.705220);
```

It returns this binary representation of a geometry data type `point`: 

```
00 00 00 00 01 01 00 00 00 20 25 76 6d 6f 80 52 c0 18 3e 22 a6 44 5a 44 40
```

In the following example, we use the ST\_GEOMETRY\_TO\_TEXT function to obtain the binary values from WKT: 

```
SELECT ST_GEOMETRY_TO_TEXT(ST_POINT(-74.006801, 40.705220)) AS WKT;
```

This query returns a WKT representation of the `point` geometry type: `1 POINT (-74.006801 40.70522)`\.

### `ST_LINE(varchar)`<a name="st-line-varchar"></a>

Returns a value in the `line` data type, which is a binary representation of the [geometry data type](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) `line`\. Example:

```
SELECT ST_Line('linestring(1 1, 2 2, 3 3)')
```

### `ST_POLYGON(varchar)`<a name="st-polygon-varchar"></a>

Returns a value in the `polygon` data type, which is a binary representation of the [geometry data type](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) `polygon`\. Example:

```
SELECT ST_POLYGON('polygon ((1 1, 1 4, 4 4, 4 1))') 
```

### `ST_GEOMETRY_TO_TEXT (varbinary)`<a name="st-geometry-to-text-varbinary"></a>

Converts each of the specified [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) to text\. Returns a value in a geometry data type, which is a WKT representation of the geometry data type\. Example:

```
SELECT ST_GEOMETRY_TO_TEXT(ST_POINT(61.56, -158.54))
```

### `ST_GEOMETRY_FROM_TEXT (varchar)`<a name="st-geometry-from-text-varchar"></a>

Converts text into a geometry data type\. Returns a value in a geometry data type, which is a binary representation of the geometry data type\. Example:

```
SELECT ST_GEOMETRY_FROM_TEXT(ST_GEOMETRY_TO_TEXT(ST_Point(1, 2)))
```

## Geospatial Relationship Functions<a name="geospatial-relationships-functions"></a>

The following functions express relationships between two different geometries that you specify as input\. They return results of type `boolean`\. The order in which you specify the pair of geometries matters: the first geometry value is called the left geometry, the second geometry value is called the right geometry\.

These functions return:
+  `TRUE` if and only if the relationship described by the function is satisfied\.
+  `FALSE` if and only if the relationship described by the function is not satisfied\.

### `ST_CONTAINS (geometry, geometry)`<a name="st-contains-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry contains the right geometry\. Examples:

```
SELECT ST_CONTAINS('POLYGON((0 2,1 1,0 -1,0 2))', 'POLYGON((-1 3,2 1,0 -3,-1 3))')
```

```
SELECT ST_CONTAINS('POLYGON((0 2,1 1,0 -1,0 2))', ST_Point(0, 0));
```

```
SELECT ST_CONTAINS(ST_GEOMETRY_FROM_TEXT('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GEOMETRY_FROM_TEXT('POLYGON((-1 3,2 1,0 -3,-1 3))'))
```

### `ST_CROSSES (geometry, geometry)`<a name="st-crosses-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry crosses the right geometry\. Example:

```
SELECT ST_CROSSES(ST_LINE('linestring(1 1, 2 2 )'), ST_LINE('linestring(0 1, 2 2)'))
```

### `ST_DISJOINT (geometry, geometry)`<a name="st-disjoint-geometry-geometry"></a>

Returns `TRUE` if and only if the intersection of the left geometry and the right geometry is empty\. Example:

```
SELECT ST_DISJOINT(ST_LINE('linestring(0 0, 0 1)'), ST_LINE('linestring(1 1, 1 0)'))
```

### `ST_EQUALS (geometry, geometry)`<a name="st-equals-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry equals the right geometry\. Example:

```
SELECT ST_EQUALS(ST_LINE('linestring( 0 0, 1 1)'), ST_LINE('linestring(1 3, 2 2)'))
```

### `ST_INTERSECTS (geometry, geometry)`<a name="st-intersects-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry intersects the right geometry\. Example:

```
SELECT ST_INTERSECTS(ST_LINE('linestring(8 7, 7 8)'), ST_POLYGON('polygon((1 1, 4 1, 4 4, 1 4))'))
```

### `ST_OVERLAPS (geometry, geometry)`<a name="st-overlaps-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry overlaps the right geometry\. Example:

```
SELECT ST_OVERLAPS(ST_POLYGON('polygon((2 0, 2 1, 3 1))'), ST_POLYGON('polygon((1 1, 1 4, 4 4, 4 1))'))
```

### `ST_RELATE (geometry, geometry)`<a name="st-relate-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry has the specified Dimensionally Extended nine\-Intersection Model \(DE\-9IM\) relationship with the right geometry\. For more information, see the Wikipedia topic [DE\-9IM](https://en.wikipedia.org/wiki/DE-9IM)\. Example:

```
SELECT ST_RELATE(ST_LINE('linestring(0 0, 3 3)'), ST_LINE('linestring(1 1, 4 4)'), 'T********')
```

### `ST_TOUCHES (geometry, geometry)`<a name="st-touches-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry touches the right geometry\.

Example:

```
SELECT ST_TOUCHES(ST_POINT(8, 8), ST_POLYGON('polygon((1  1, 1  4, 4  4, 4 1))'))
```

### `ST_WITHIN (geometry, geometry)`<a name="st-within-geometry-geometry"></a>

Returns `TRUE` if and only if the left geometry is within the right geometry\.

Example:

```
SELECT ST_WITHIN(ST_POINT(8, 8), ST_POLYGON('polygon((1  1, 1  4, 4  4, 4 1))'))
```

## Operation Functions<a name="operations-functions"></a>

Use operation functions to perform operations on geometry data type values\. For example, you can obtain the boundaries of a single geometry data type; intersections between two geometry data types; difference between left and right geometries, where each is of the same geometry data type; or an exterior buffer or ring around a particular geometry data type\.

All operation functions take as an input one of the geometry data types and return their binary representations\.

### `ST_BOUNDARY (geometry)`<a name="st-boundary-geometry"></a>

Takes as an input one of the geometry data types, and returns a binary representation of the `boundary` geometry data type\.

Examples:

```
SELECT ST_BOUNDARY(ST_LINE('linestring(0 1, 1 0)')))
```

```
SELECT ST_BOUNDARY(ST_POLYGON('polygon((1  1, 1  4, 4  4, 4 1))'))
```

### `ST_BUFFER (geometry, double)`<a name="st-buffer-geometry-double"></a>

Takes as an input one of the geometry data types, such as point, line, polygon, multiline, or multipolygon, and a distance as type `double`\)\. Returns a binary representation of the geometry data type buffered by the specified distance \(or radius\)\. Example:

```
SELECT ST_BUFFER(ST_Point(1, 2), 2.0)
```

In the following example, the map coordinates are specified in longitude and latitude, and the value `.072284`, which is the buffer distance, is specified in angular units as decimal degrees:

```
ST_BUFFER(ST_POINT(-74.006801, 40.705220), .072284)
```

### `ST_DIFFERENCE (geometry, geometry)`<a name="st-difference-geometry-geometry"></a>

Returns a binary representation of a difference between the left geometry and right geometry\. Example:

```
SELECT ST_GEOMETRY_TO_TEXT(ST_DIFFERENCE(ST_POLYGON('polygon((0 0, 0 10, 10 10, 10 0))'), ST_POLYGON('polygon((0 0, 0 5, 5 5, 5 0))')))
```

### `ST_ENVELOPE (geometry)`<a name="st-envelope-geometry"></a>

Takes as an input `line`, `polygon`, `multiline`, and `multipolygon` geometry data types\. Does not support `point` geometry data type\. Returns a binary representation of an envelope, where an envelope is a rectangle around the specified geometry data type\. Examples:

```
SELECT ST_ENVELOPE(ST_LINE('linestring(0 1, 1 0)'))
```

```
SELECT ST_ENVELOPE(ST_POLYGON('polygon((1  1, 1  4, 4  4, 4 1))'))
```

### `ST_EXTERIOR_RING (geometry)`<a name="st-exterior-ring-geometry"></a>

Returns a binary representation of the exterior ring of the input type `polygon`\. Examples:

```
SELECT ST_EXTERIOR_RING(ST_POLYGON(1,1, 1,4, 4,1))
```

```
SELECT ST_EXTERIOR_RING(ST_POLYGON('polygon ((0 0, 8 0, 0 8, 0 0), (1 1, 1 5, 5 1, 1 1))'))
```

### `ST_INTERSECTION (geometry, geometry)`<a name="st-intersection-geometry-geometry"></a>

Returns a binary representation of the intersection of the left geometry and right geometry\. Examples:

```
SELECT ST_INTERSECTION(ST_POINT(1,1), ST_POINT(1,1))
```

```
SELECT ST_INTERSECTION(ST_LINE('linestring(0 1, 1 0)'), ST_POLYGON('polygon((1  1, 1  4, 4  4, 4 1))'))
```

```
SELECT ST_GEOMETRY_TO_TEXT(ST_INTERSECTION(ST_POLYGON('polygon((2 0, 2 3, 3 0))'), ST_POLYGON('polygon((1 1, 4 1, 4 4, 1 4))')))
```

### `ST_SYMMETRIC_DIFFERENCE (geometry, geometry)`<a name="st-symmetric-difference-geometry-geometry"></a>

Returns a binary representation of the geometrically symmetric difference between left geometry and right geometry\. Example:

```
SELECT ST_GEOMETRY_TO_TEXT(ST_SYMMETRIC_DIFFERENCE(ST_LINE('linestring(0 2, 2 2)'), ST_LINE('linestring(1 2, 3 2)')))
```

## Accessor Functions<a name="accessors-functions"></a>

Accessor functions are useful to obtain values in types `varchar`, `bigint`, or `double` from different `geometry` data types, where `geometry` is any of the geometry data types supported in Athena: `point`, `line`, `polygon`, `multiline`, and `multipolygon`\. For example, you can obtain an area of a `polygon` geometry data type, maximum and minimum X and Y values for a specified geometry data type, obtain the length of a `line`, or receive the number of points in a specified geometry data type\.

### `ST_AREA (geometry)`<a name="st-area-geometry"></a>

Takes as an input a geometry data type `polygon` and returns an area in type `double`\. Example:

```
SELECT ST_AREA(ST_POLYGON('polygon((1 1, 4 1, 4 4, 1 4))'))
```

### `ST_CENTROID (geometry)`<a name="st-centroid-geometry"></a>

Takes as an input a [geometry data type](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) `polygon`, and returns a `point` that is the center of the polygon's envelope in type `varchar`\. Examples:

```
SELECT ST_CENTROID(ST_GEOMETRY_FROM_TEXT('polygon ((0 0, 3 6, 6 0, 0 0))'))
```

```
SELECT ST_GEOMETRY_TO_TEXT(ST_CENTROID(ST_ENVELOPE(ST_GEOMETRY_FROM_TEXT('POINT (53 27)'))))
```

### `ST_COORDINATE_DIMENSION (geometry)`<a name="st-coordinate-dimension-geometry"></a>

Takes as input one of the supported [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types), and returns the count of coordinate components in type `bigint`\. Example:

```
SELECT ST_COORDINATE_DIMENSION(ST_POINT(1.5,2.5))
```

### `ST_DIMENSION (geometry)`<a name="st-dimension-geometry"></a>

Takes as an input one of the supported [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types), and returns the spatial dimension of a geometry in type `bigint`\. Example:

```
SELECT ST_DIMENSION(ST_POLYGON('polygon((1 1, 4 1, 4 4, 1 4))'))
```

### `ST_DISTANCE (geometry, geometry)`<a name="st-distance-geometry-geometry"></a>

Returns the distance in type `double` between the left geometry and the right geometry\. Example:

```
SELECT ST_DISTANCE(ST_POINT(0.0,0.0), ST_POINT(3.0,4.0))
```

### `ST_IS_CLOSED (geometry)`<a name="st-is-closed-geometry"></a>

Returns `TRUE` \(type `boolean`\) if and only if the line is closed\. Example:

```
SELECT ST_IS_CLOSED(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_IS_EMPTY (geometry)`<a name="st-is-empty-geometry"></a>

Takes as an input only `line` and `multiline` [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types)\. Returns `TRUE` \(type `boolean`\) if and only if the specified geometry is empty, in other words, when the `line` start and end values co\-inside\. Example:

```
SELECT ST_IS_EMPTY(ST_POINT(1.5, 2.5))
```

### `ST_IS_RING (geometry)`<a name="st-is-ring-geometry"></a>

Returns `TRUE` \(type `boolean`\) if and only if the `line` type is closed and simple\. Example:

```
SELECT ST_IS_RING(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_LENGTH (geometry)`<a name="st-length-geometry"></a>

Returns the length of `line` in type `double`\. Example:

```
SELECT ST_LENGTH(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_MAX_X (geometry)`<a name="st-max-x-geometry"></a>

Returns the maximum X coordinate of a geometry in type `double`\. Example:

```
SELECT ST_MAX_X(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_MAX_Y (geometry)`<a name="st-max-y-geometry"></a>

Returns the maximum Y coordinate of a geometry in type `double`\. Example:

```
SELECT ST_MAX_Y(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_MIN_X (geometry)`<a name="st-min-x-geometry"></a>

Returns the minimum X coordinate of a geometry in type `double`\. Example:

```
SELECT ST_MIN_X(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_MIN_Y (geometry)`<a name="st-min-y-geometry"></a>

Returns the minimum Y coordinate of a geometry in type `double`\. Example:

```
SELECT ST_MAX_Y(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_START_POINT (geometry)`<a name="st-start-point-geometry"></a>

Returns the first point of a `line` geometry data type in type `point`\. Example:

```
SELECT ST_START_POINT(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_END_POINT (geometry)`<a name="st-end-point-geometry"></a>

Returns the last point of a `line` geometry data type in type `point`\. Example:

```
SELECT ST_END_POINT(ST_LINE('linestring(0 2, 2 2)'))
```

### `ST_X (point)`<a name="st-x-point"></a>

Returns the X coordinate of a point in type `double`\. Example:

```
SELECT ST_X(ST_POINT(1.5, 2.5))
```

### `ST_Y (point)`<a name="st-y-point"></a>

Returns the Y coordinate of a point in type `double`\. Example:

```
SELECT ST_Y(ST_POINT(1.5, 2.5))
```

### `ST_POINT_NUMBER (geometry)`<a name="st-point-number-geometry"></a>

Returns the number of points in the geometry in type `bigint`\. Example:

```
SELECT ST_POINT_NUMBER(ST_POINT(1.5, 2.5))
```

### `ST_INTERIOR_RING_NUMBER (geometry)`<a name="st-interior-ring-number-geometry"></a>

Returns the number of interior rings in the `polygon` geometry in type `bigint`\. Example:

```
SELECT ST_INTERIOR_RING_NUMBER(ST_POLYGON('polygon ((0 0, 8 0, 0 8, 0 0), (1 1, 1 5, 5 1, 1 1))'))
```