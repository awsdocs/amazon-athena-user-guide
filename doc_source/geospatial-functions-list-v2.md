# Geospatial Functions in Athena engine version 2<a name="geospatial-functions-list-v2"></a>

This topic lists the ESRI geospatial functions that are supported in Athena engine version 2\. Currently, Athena engine version 2 is supported in the US East \(N\. Virginia\), US East \(Ohio\), and US West \(Oregon\) Regions\. For information about Athena engine versions, see [Athena Engine Versioning](engine-versions.md)\.

**Changes in Athena engine version 2**
+ The input and output types for some functions have changed\. Most notably, the `VARBINARY` type is no longer directly supported for input\. For more information, see [Changes to Geospatial Functions](engine-versions-reference.md#engine-versions-reference-0002-changes-to-geospatial-functions)\.
+ The names of some geospatial functions have changed since Athena engine version 1\. For more information, see [Geospatial Function Name Changes in Athena engine version 2](geospatial-functions-list-v2-function-name-changes-and-new-functions.md#geospatial-functions-list-v2-function-name-changes)\.
+ New functions have been added\. For more information, see [New Geospatial Functions in Athena engine version 2](geospatial-functions-list-v2-function-name-changes-and-new-functions.md#geospatial-functions-list-v2-new-functions)\.

Athena supports four types of geospatial functions:
+  [Constructor Functions](#geospatial-functions-list-v2-constructors-functions) 
+  [Geospatial Relationship Functions](#geospatial-functions-list-v2-geospatial-relationships-functions) 
+  [Operation Functions](#geospatial-functions-list-v2-operations-functions) 
+  [Accessor Functions](#geospatial-functions-list-v2-accessors-functions) 

## Constructor Functions<a name="geospatial-functions-list-v2-constructors-functions"></a>

Use constructor functions to obtain binary representations of `point`, `line`, or `polygon` geometry data types\. You can also use these functions to convert binary data to text, and obtain binary values for geometry data that is expressed as Well\-Known Text \(WKT\)\. 

### `ST_AsBinary(geometry)`<a name="geospatial-functions-list-v2-st-asbinary"></a>

Returns a varbinary data type that contains the WKB representation of the specified geometry\. Example:

```
SELECT ST_AsBinary(ST_Point(61.56, -158.54))
```

### `ST_AsText(geometry)`<a name="geospatial-functions-list-v2-st-geometry-astext"></a>

Converts each of the specified [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) to text\. Returns a value in a varchar data type, which is a WKT representation of the geometry data type\. Example:

```
SELECT ST_AsText(ST_Point(61.56, -158.54))
```

### `ST_GeomAsLegacyBinary(geometry)`<a name="geospatial-functions-list-v2-st-geomaslegacybinary"></a>

Returns an Athena engine version 1 varbinary from the specified geometry\. Example:

```
SELECT ST_GeomAsLegacyBinary(ST_Point(61.56, -158.54)
```

### `ST_GeometryFromText(varchar)`<a name="geospatial-functions-list-v2-st-geometryfromtext"></a>

Converts text in WKT format into a geometry data type\. Returns a value in a geometry data type\. Example:

```
SELECT ST_GeometryFromText(ST_AsText(ST_Point(1, 2)))
```

### `ST_GeomFromBinary(varbinary)`<a name="geospatial-functions-list-v2-st-geomfrombinary"></a>

Returns a geometry type object from a WKB representation\. Example:

```
SELECT ST_GeomFromBinary(ST_AsBinary(ST_Point(61.56, -158.54)))
```

### `ST_GeomFromLegacyBinary(varbinary)`<a name="geospatial-functions-list-v2-st-geomfromlegacybinary"></a>

Returns a geometry type object from an Athena engine version 1 varbinary type\. Example:

```
SELECT ST_GeomFromLegacyBinary(ST_GeomAsLegacyBinary(ST_Point(61.56, -158.54)))
```

### `ST_LineFromText(varchar)`<a name="geospatial-functions-list-v2-st-linefromtext"></a>

Returns a value in the [geometry data type](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) `line`\. Example:

```
SELECT ST_Line('linestring(1 1, 2 2, 3 3)')
```

### `ST_LineString(array(point))`<a name="geospatial-functions-list-v2-st-linestring"></a>

Returns a `LineString` geometry type formed from an array of point geometry types\. If there are fewer than two non\-empty points in the specified array, an empty `LineString` is returned\. Throws an exception if any element in the array is null, empty, or the same as the previous one\. The returned geometry may not be simple\. Depending on the input specfied, the returned geometry can self\-intersect or contain duplicate vertexes\. Example:

```
SELECT ST_LineString(ARRAY[ST_Point(61.56, -158.54), ST_Point(61.56, -158.55)])
```

### `ST_MultiPoint(array(point))`<a name="geospatial-functions-list-v2-st-multipoint"></a>

Returns a `MultiPoint` geometry object formed from the specified points\. Returns null if the specified array is empty\. Throws an exception if any element in the array is null or empty\. The returned geometry may not be simple and can contain duplicate points if the specified array has duplicates\. Example:

```
SELECT ST_MultiPoint(ARRAY[ST_Point(61.56, -158.54), ST_Point(61.56, -158.55)])
```

### `ST_Point(double, double)`<a name="geospatial-functions-list-v2-st-point"></a>

Returns a geometry type `point` object\. For the input data values to this function, use geometric values, such as values in the Universal Transverse Mercator \(UTM\) Cartesian coordinate system, or geographic map units \(longitude and latitude\) in decimal degrees\. The longitude and latitude values use the World Geodetic System, also known as WGS 1984, or EPSG:4326\. WGS 1984 is the coordinate system used by the Global Positioning System \(GPS\)\. 

For example, in the following notation, the map coordinates are specified in longitude and latitude, and the value `.072284`, which is the buffer distance, is specified in angular units as decimal degrees:

```
SELECT ST_Buffer(ST_Point(-74.006801, 40.705220), .072284)
```

Syntax:

```
SELECT ST_Point(longitude, latitude) FROM earthquakes LIMIT 1
```

The following example uses specific longitude and latitude coordinates:

```
SELECT ST_Point(61.56, -158.54)
FROM earthquakes
LIMIT 1
```

The next example uses specific longitude and latitude coordinates: 

```
SELECT ST_Point(-74.006801, 40.705220)
```

The following example uses the `ST_AsText` function to obtain the geometry from WKT:

```
SELECT ST_AsText(ST_Point(-74.006801, 40.705220)) AS WKT
```

### `ST_Polygon(varchar)`<a name="geospatial-functions-list-v2-st-polygon"></a>

Using the sequence of the ordinates provided clockwise, left to right, returns a [geometry data type](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) `polygon`\. In Athena engine version 2, only polygons are accepted as inputs\. Example:

```
SELECT ST_Polygon('polygon ((1 1, 1 4, 4 4, 4 1))') 
```

### `to_geometry(sphericalGeography)`<a name="geospatial-functions-list-v2-to-geometry"></a>

Returns a geometry object from the specified spherical geography object\. Example:

```
SELECT to_geometry(to_spherical_geography(ST_Point(61.56, -158.54)))
```

### `to_spherical_geography(geometry)`<a name="geospatial-functions-list-v2-to-spherical-geography"></a>

Returns a spherical geography object from the specified geometry\. Use this function to convert a geometry object to a spherical geography object on the sphere of the Earth’s radius\. This function can be used only on `POINT`, `MULTIPOINT`, `LINESTRING`, `MULTILINESTRING`, `POLYGON`, and `MULTIPOLYGON` geometries defined in 2D space or a `GEOMETRYCOLLECTION` of such geometries\. For each point of the specified geometry, the function verifies that `point.x` is within `[-180.0, 180.0]` and `point.y` is within `[-90.0, 90.0]`\. The function uses these points as longitude and latitude degrees to construct the shape of the `sphericalGeography` result\.

Example:

```
SELECT to_spherical_geography(ST_Point(61.56, -158.54))
```

## Geospatial Relationship Functions<a name="geospatial-functions-list-v2-geospatial-relationships-functions"></a>

The following functions express relationships between two different geometries that you specify as input and return results of type `boolean`\. The order in which you specify the pair of geometries matters: the first geometry value is called the left geometry, the second geometry value is called the right geometry\.

These functions return:
+  `TRUE` if and only if the relationship described by the function is satisfied\.
+  `FALSE` if and only if the relationship described by the function is not satisfied\.

### `ST_Contains(geometry, geometry)`<a name="geospatial-functions-list-v2-st-contains"></a>

Returns `TRUE` if and only if the left geometry contains the right geometry\. Examples:

```
SELECT ST_Contains('POLYGON((0 2,1 1,0 -1,0 2))', 'POLYGON((-1 3,2 1,0 -3,-1 3))')
```

```
SELECT ST_Contains('POLYGON((0 2,1 1,0 -1,0 2))', ST_Point(0, 0))
```

```
SELECT ST_Contains(ST_GeometryFromText('POLYGON((0 2,1 1,0 -1,0 2))'), ST_GeometryFromText('POLYGON((-1 3,2 1,0 -3,-1 3))'))
```

### `ST_Crosses(geometry, geometry)`<a name="geospatial-functions-list-v2-st-crosses"></a>

Returns `TRUE` if and only if the left geometry crosses the right geometry\. Example:

```
SELECT ST_Crosses(ST_Line('linestring(1 1, 2 2 )'), ST_Line('linestring(0 1, 2 2)'))
```

### `ST_Disjoint(geometry, geometry)`<a name="geospatial-functions-list-v2-st-disjoint"></a>

Returns `TRUE` if and only if the intersection of the left geometry and the right geometry is empty\. Example:

```
SELECT ST_Disjoint(ST_Line('linestring(0 0, 0 1)'), ST_Line('linestring(1 1, 1 0)'))
```

### `ST_Equals(geometry, geometry)`<a name="geospatial-functions-list-v2-st-equals"></a>

Returns `TRUE` if and only if the left geometry equals the right geometry\. Example:

```
SELECT ST_Equals(ST_Line('linestring( 0 0, 1 1)'), ST_Line('linestring(1 3, 2 2)'))
```

### `ST_Intersects(geometry, geometry)`<a name="geospatial-functions-list-v2-st-intersects"></a>

Returns `TRUE` if and only if the left geometry intersects the right geometry\. Example:

```
SELECT ST_Intersects(ST_Line('linestring(8 7, 7 8)'), ST_Polygon('polygon((1 1, 4 1, 4 4, 1 4))'))
```

### `ST_Overlaps(geometry, geometry)`<a name="geospatial-functions-list-v2-st-overlaps"></a>

Returns `TRUE` if and only if the left geometry overlaps the right geometry\. Example:

```
SELECT ST_Overlaps(ST_Polygon('polygon((2 0, 2 1, 3 1))'), ST_Polygon('polygon((1 1, 1 4, 4 4, 4 1))'))
```

### `ST_Relate(geometry, geometry, varchar)`<a name="geospatial-functions-list-v2-st-relate"></a>

Returns `TRUE` if and only if the left geometry has the specified dimensionally extended nine\-intersection model \([DE\-9IM](https://en.wikipedia.org/wiki/DE-9IM)\) relationship with the right geometry\. The third \(`varchar`\) input takes the relationship\. Example:

```
SELECT ST_Relate(ST_Line('linestring(0 0, 3 3)'), ST_Line('linestring(1 1, 4 4)'), 'T********')
```

### `ST_Touches(geometry, geometry)`<a name="geospatial-functions-list-v2-st-touches"></a>

Returns `TRUE` if and only if the left geometry touches the right geometry\.

Example:

```
SELECT ST_Touches(ST_Point(8, 8), ST_Polygon('polygon((1  1, 1  4, 4  4, 4 1))'))
```

### `ST_Within(geometry, geometry)`<a name="geospatial-functions-list-v2-st-within"></a>

Returns `TRUE` if and only if the left geometry is within the right geometry\.

Example:

```
SELECT ST_Within(ST_Point(8, 8), ST_Polygon('polygon((1  1, 1  4, 4  4, 4 1))'))
```

## Operation Functions<a name="geospatial-functions-list-v2-operations-functions"></a>

Use operation functions to perform operations on geometry data type values\. For example, you can obtain the boundaries of a single geometry data type; intersections between two geometry data types; difference between left and right geometries, where each is of the same geometry data type; or an exterior buffer or ring around a particular geometry data type\.

### `geometry_union(array(geometry))`<a name="geospatial-functions-list-v2-geometry-union"></a>

Returns a geometry that represents the point set union of the specified geometries\. Example:

```
SELECT geometry_union(ARRAY[ST_Point(61.56, -158.54), ST_Point(61.56, -158.55)])
```

### `ST_Boundary(geometry)`<a name="geospatial-functions-list-v2-st-boundary"></a>

Takes as an input one of the geometry data types and returns the `boundary` geometry data type\.

Examples:

```
SELECT ST_Boundary(ST_Line('linestring(0 1, 1 0)')))
```

```
SELECT ST_Boundary(ST_Polygon('polygon((1  1, 1  4, 4  4, 4 1))'))
```

### `ST_Buffer(geometry, double)`<a name="geospatial-functions-list-v2-st-buffer"></a>

Takes as an input one of the geometry data types, such as point, line, polygon, multiline, or multipolygon, and a distance as type `double`\)\. Returns the geometry data type buffered by the specified distance \(or radius\)\. Example:

```
SELECT ST_Buffer(ST_Point(1, 2), 2.0)
```

In the following example, the map coordinates are specified in longitude and latitude, and the value `.072284`, which is the buffer distance, is specified in angular units as decimal degrees:

```
SELECT ST_Buffer(ST_Point(-74.006801, 40.705220), .072284)
```

### `ST_Difference(geometry, geometry)`<a name="geospatial-functions-list-v2-st-difference"></a>

Returns a geometry of the difference between the left geometry and right geometry\. Example:

```
SELECT ST_AsText(ST_Difference(ST_Polygon('polygon((0 0, 0 10, 10 10, 10 0))'), ST_Polygon('polygon((0 0, 0 5, 5 5, 5 0))')))
```

### `ST_Envelope(geometry)`<a name="geospatial-functions-list-v2-st-envelope"></a>

Takes as an input `line`, `polygon`, `multiline`, and `multipolygon` geometry data types\. Does not support `point` geometry data type\. Returns the envelope as a geometry, where an envelope is a rectangle around the specified geometry data type\. Examples:

```
SELECT ST_Envelope(ST_Line('linestring(0 1, 1 0)'))
```

```
SELECT ST_Envelope(ST_Polygon('polygon((1  1, 1  4, 4  4, 4 1))'))
```

### `ST_EnvelopeAsPts(geometry)`<a name="geospatial-functions-list-v2-st-envelopeaspts"></a>

Returns an array of two points that represent the lower left and upper right corners of a geometry's bounding rectangular polygon\. Returns null if the specified geometry is empty\. Example:

```
SELECT ST_EnvelopeAsPts(ST_Point(61.56, -158.54))
```

### `ST_ExteriorRing(geometry)`<a name="geospatial-functions-list-v2-st-exterior-ring"></a>

Returns the geometry of the exterior ring of the input type `polygon`\. In Athena engine version 2, polygons are the only geometries accepted as inputs\. Examples:

```
SELECT ST_ExteriorRing(ST_Polygon(1,1, 1,4, 4,1))
```

```
SELECT ST_ExteriorRing(ST_Polygon('polygon ((0 0, 8 0, 0 8, 0 0), (1 1, 1 5, 5 1, 1 1))'))
```

### `ST_Intersection(geometry, geometry)`<a name="geospatial-functions-list-v2-st-intersection"></a>

Returns the geometry of the intersection of the left geometry and right geometry\. Examples:

```
SELECT ST_Intersection(ST_Point(1,1), ST_Point(1,1))
```

```
SELECT ST_Intersection(ST_Line('linestring(0 1, 1 0)'), ST_Polygon('polygon((1  1, 1  4, 4  4, 4 1))'))
```

```
SELECT ST_AsText(ST_Intersection(ST_Polygon('polygon((2 0, 2 3, 3 0))'), ST_Polygon('polygon((1 1, 4 1, 4 4, 1 4))')))
```

### `ST_SymDifference(geometry, geometry)`<a name="geospatial-functions-list-v2-st-symdifference"></a>

Returns the geometry of the geometrically symmetric difference between the left geometry and the right geometry\. Example:

```
SELECT ST_AsText(ST_SymDifference(ST_Line('linestring(0 2, 2 2)'), ST_Line('linestring(1 2, 3 2)')))
```

### `ST_Union(geometry, geometry)`<a name="geospatial-functions-list-v2-st-union"></a>

Returns a geometry data type that represents the point set union of the specified geometries\. Example:

```
SELECT ST_Union(ST_Point(61.56, -158.54),ST_LineString(array[ST_Point(1,2), ST_Point(3,4)]))
```

## Accessor Functions<a name="geospatial-functions-list-v2-accessors-functions"></a>

Accessor functions are useful to obtain values in types `varchar`, `bigint`, or `double` from different `geometry` data types, where `geometry` is any of the geometry data types supported in Athena: `point`, `line`, `polygon`, `multiline`, and `multipolygon`\. For example, you can obtain an area of a `polygon` geometry data type, maximum and minimum X and Y values for a specified geometry data type, obtain the length of a `line`, or receive the number of points in a specified geometry data type\.

### `geometry_invalid_reason(geometry)`<a name="geospatial-functions-list-v2-geometry-invalid-reason"></a>

Returns, in a varchar data type, the reason why the specified geometry is not valid or not simple\. If the specified geometry is neither valid nor simple, returns the reason why it is not valid\. If the specified geometry is valid and simple, returns null\. Example:

```
SELECT geometry_invalid_reason(ST_Point(61.56, -158.54))
```

### `great_circle_distance(latitude1, longitude1, latitude2, longitude2)`<a name="geospatial-functions-list-v2-great-circle-distance"></a>

Returns, as a double, the great\-circle distance between two points on Earth’s surface in kilometers\. Example:

```
SELECT great_circle_distance(36.12, -86.67, 33.94, -118.40)
```

### `line_locate_point(lineString, point)`<a name="geospatial-functions-list-v2-line-locate-point"></a>

Returns a double between 0 and 1 that represents the location of the closest point on the specified line string to the specified point as a fraction of total 2d line length\.

Returns null if the specified line string or point is empty or null\. Example:

```
SELECT line_locate_point(ST_GeometryFromText('LINESTRING (0 0, 0 1)'), ST_Point(0, 0.2))
```

### `simplify_geometry(geometry, double)`<a name="geospatial-functions-list-v2-simplify-geometry"></a>

Uses the [Ramer\-Douglas\-Peucker algorithm](https://en.wikipedia.org/wiki/Ramer%E2%80%93Douglas%E2%80%93Peucker_algorithm) to return a geometry data type that is a simplified version of the specified geometry\. Avoids creating derived geometries \(in particular, polygons\) that are invalid\. Example:

```
SELECT simplify_geometry(ST_GeometryFromText('POLYGON ((1 0, 2 1, 3 1, 3 1, 4 1, 1 0))'), 1.5)
```

### `ST_Area(geometry)`<a name="geospatial-functions-list-v2-st-area"></a>

Takes as an input a geometry data type `polygon` and returns an area in type `double`\. Example:

```
SELECT ST_Area(ST_Polygon('polygon((1 1, 4 1, 4 4, 1 4))'))
```

### `ST_Centroid(geometry)`<a name="geospatial-functions-list-v2-st-centroid"></a>

Takes as an input a [geometry data type](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types) `polygon`, and returns a `point` geometry data type that is the center of the polygon's envelope\. Examples:

```
SELECT ST_Centroid(ST_GeometryFromText('polygon ((0 0, 3 6, 6 0, 0 0))'))
```

```
SELECT ST_AsText(ST_Centroid(ST_Envelope(ST_GeometryFromText('POINT (53 27)'))))
```

### `ST_ConvexHull(geometry)`<a name="geospatial-functions-list-v2-st-convexhull"></a>

Returns a geometry data type that is the smallest convex geometry that encloses all geometries in the specified input\. Example:

```
SELECT ST_ConvexHull(ST_Point(61.56, -158.54))
```

### `ST_CoordDim(geometry)`<a name="geospatial-functions-list-v2-st-coordim"></a>

Takes as input one of the supported [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types), and returns the count of coordinate components in the type `tinyint`\. Example:

```
SELECT ST_CoordDim(ST_Point(1.5,2.5))
```

### `ST_Dimension(geometry)`<a name="geospatial-functions-list-v2-st-dimension"></a>

Takes as an input one of the supported [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types), and returns the spatial dimension of a geometry in type `tinyint`\. Example:

```
SELECT ST_Dimension(ST_Polygon('polygon((1 1, 4 1, 4 4, 1 4))'))
```

### `ST_Distance(geometry, geometry)`<a name="geospatial-functions-list-v2-st-distance"></a>

Returns, based on spatial ref, a double containing the two\-dimensional minimum Cartesian distance between two geometries in projected units\. In Athena engine version 2, returns null if one of the inputs is an empty geometry\. Example:

```
SELECT ST_Distance(ST_Point(0.0,0.0), ST_Point(3.0,4.0))
```

### `ST_Distance(sphericalGeography, sphericalGeography)`<a name="geospatial-functions-list-v2-st-distance-spherical"></a>

Returns, as a double, the great\-circle distance between two spherical geography points in meters\. Example:

```
SELECT ST_Distance(to_spherical_geography(ST_Point(61.56, -86.67)),to_spherical_geography(ST_Point(61.56, -86.68)))
```

### `ST_EndPoint(geometry)`<a name="geospatial-functions-list-v2-st-end-point"></a>

Returns the last point of a `line` geometry data type in a `point` geometry data type\. Example:

```
SELECT ST_EndPoint(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_Geometries(geometry)`<a name="geospatial-functions-list-v2-st-geometries"></a>

Returns an array of geometries in the specified collection\. If the specified geometry is not a multi\-geometry, returns a one\-element array\. If the specified geometry is empty, returns null\.

For example, given a `MultiLineString` object, `ST_Geometries` creates an array of `LineString` objects\. Given a `GeometryCollection` object, `ST_Geometries` returns an un\-flattened array of its constituents\. Example:

```
SELECT ST_Geometries(GEOMETRYCOLLECTION(MULTIPOINT(0 0, 1 1), GEOMETRYCOLLECTION(MULTILINESTRING((2 2, 3 3)))))
```

Result:

```
array[MULTIPOINT(0 0, 1 1),GEOMETRYCOLLECTION(MULTILINESTRING((2 2, 3 3)))]
```

### `ST_GeometryN(geometry, index)`<a name="geospatial-functions-list-v2-st-geometryn"></a>

Returns, as a geometry data type, the geometry element at a specified integer index\. Indices start at 1\. If the specified geometry is a collection of geometries \(for example, a `GEOMETRYCOLLECTION` or `MULTI*` object\), returns the geometry at the specified index\. If the specified index is less than 1 or greater than the total number of elements in the collection, returns null\. To find the total number of elements, use [`ST_NumGeometries`](#geospatial-functions-list-v2-st-numgeometries)\. Singular geometries \(for example, `POINT`, `LINESTRING`, or `POLYGON`\), are treated as collections of one element\. Empty geometries are treated as empty collections\. Example:

```
SELECT ST_GeometryN(ST_Point(61.56, -158.54),1)
```

### `ST_GeometryType(geometry)`<a name="geospatial-functions-list-v2-st-geometrytype"></a>

Returns, as a varchar, the type of the geometry\. Example:

```
SELECT ST_GeometryType(ST_Point(61.56, -158.54))
```

### `ST_InteriorRingN(geometry, index)`<a name="geospatial-functions-list-v2-st-interiorringn"></a>

Returns the interior ring element at the specified index \(indices start at 1\)\. If the given index is less than 1 or greater than the total number of interior rings in the specified geometry, returns null\. Throws an error if the specified geometry is not a polygon\. To find the total number of elements, use [`ST_NumInteriorRing`](#geospatial-functions-list-v2-st-numinteriorring)\. Example:

```
SELECT ST_InteriorRingN(st_polygon('polygon ((0 0, 1 0, 1 1, 0 1, 0 0))'),1)
```

### `ST_InteriorRings(geometry)`<a name="geospatial-functions-list-v2-st-interiorrings"></a>

Returns a geometry array of all interior rings found in the specified geometry, or an empty array if the polygon has no interior rings\. If the specified geometry is empty, returns null\. If the specified geometry is not a polygon, throws an error\. Example:

```
SELECT ST_InteriorRings(st_polygon('polygon ((0 0, 1 0, 1 1, 0 1, 0 0))'))
```

### `ST_IsClosed(geometry)`<a name="geospatial-functions-list-v2-st-isclosed"></a>

Takes as an input only `line` and `multiline` [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types)\. Returns `TRUE` \(type `boolean`\) if and only if the line is closed\. Example:

```
SELECT ST_IsClosed(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_IsEmpty(geometry)`<a name="geospatial-functions-list-v2-st-isempty"></a>

Takes as an input only `line` and `multiline` [geometry data types](geospatial-input-data-formats-supported-geometry-types.md#geometry-data-types)\. Returns `TRUE` \(type `boolean`\) if and only if the specified geometry is empty, in other words, when the `line` start and end values coincide\. Example:

```
SELECT ST_IsEmpty(ST_Point(1.5, 2.5))
```

### `ST_IsRing(geometry)`<a name="geospatial-functions-list-v2-st-isring"></a>

Returns `TRUE` \(type `boolean`\) if and only if the `line` type is closed and simple\. Example:

```
SELECT ST_IsRing(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_IsSimple(geometry)`<a name="geospatial-functions-list-v2-st-issimple"></a>

Returns true if the specified geometry has no anomalous geometric points \(for example, self intersection or self tangency\)\. To determine why the geometry is not simple, use [`geometry_invalid_reason()`](#geospatial-functions-list-v2-geometry-invalid-reason)\. Example:

```
SELECT ST_IsSimple(ST_LineString(array[ST_Point(1,2), ST_Point(3,4)]))
```

### `ST_IsValid(geometry)`<a name="geospatial-functions-list-v2-st-isvalid"></a>

Returns true if and only if the specified geometry is well formed\. To determine why the geometry is not well formed, use [`geometry_invalid_reason()`](#geospatial-functions-list-v2-geometry-invalid-reason)\. Example:

```
SELECT ST_IsValid(ST_Point(61.56, -86.68))
```

### `ST_Length(geometry)`<a name="geospatial-functions-list-v2-st-length"></a>

Returns the length of `line` in type `double`\. Example:

```
SELECT ST_Length(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_NumGeometries(geometry)`<a name="geospatial-functions-list-v2-st-numgeometries"></a>

Returns, as an integer, the number of geometries in the collection\. If the geometry is a collection of geometries \(for example, a `GEOMETRYCOLLECTION` or `MULTI*` object\), returns the number of geometries\. Single geometries return 1; empty geometries return 0\. An empty geometry in a `GEOMETRYCOLLECTION` object counts as one geometry\. For example, the following example evaluates to 1:

```
ST_NumGeometries(ST_GeometryFromText('GEOMETRYCOLLECTION(MULTIPOINT EMPTY)'))
```

### `ST_NumInteriorRing(geometry)`<a name="geospatial-functions-list-v2-st-numinteriorring"></a>

Returns the number of interior rings in the `polygon` geometry in type `bigint`\. Example:

```
SELECT ST_NumInteriorRing(ST_Polygon('polygon ((0 0, 8 0, 0 8, 0 0), (1 1, 1 5, 5 1, 1 1))'))
```

### `ST_NumPoints(geometry)`<a name="geospatial-functions-list-v2-st-numpoints"></a>

Returns the number of points in the geometry in type `bigint`\. Example:

```
SELECT ST_NumPoints(ST_Point(1.5, 2.5))
```

### `ST_PointN(lineString, index)`<a name="geospatial-functions-list-v2-st-pointn"></a>

Returns, as a point geometry data type, the vertex of the specified line string at the specified integer index\. Indices start at 1\. If the given index is less than 1 or greater than the total number of elements in the collection, returns null\. To find the total number of elements, use [`ST_NumPoints`](#geospatial-functions-list-v2-st-numpoints)\. Example:

```
SELECT ST_PointN(ST_LineString(array[ST_Point(1,2), ST_Point(3,4)]),1)
```

### `ST_Points(geometry)`<a name="geospatial-functions-list-v2-st-points"></a>

Returns an array of points from the specified line string geometry object\. Example:

```
SELECT ST_Points(ST_LineString(array[ST_Point(1,2), ST_Point(3,4)]))
```

### `ST_StartPoint(geometry)`<a name="geospatial-functions-list-v2-st-startpoint"></a>

Returns the first point of a `line` geometry data type in a `point` geometry data type\. Example:

```
SELECT ST_StartPoint(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_X(point)`<a name="geospatial-functions-list-v2-st-x"></a>

Returns the X coordinate of a point in type `double`\. Example:

```
SELECT ST_X(ST_Point(1.5, 2.5))
```

### `ST_XMax(geometry)`<a name="geospatial-functions-list-v2-st-xmax"></a>

Returns the maximum X coordinate of a geometry in type `double`\. Example:

```
SELECT ST_XMax(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_XMin(geometry)`<a name="geospatial-functions-list-v2-st-xmin"></a>

Returns the minimum X coordinate of a geometry in type `double`\. Example:

```
SELECT ST_XMin(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_Y(point)`<a name="geospatial-functions-list-v2-st-y"></a>

Returns the Y coordinate of a point in type `double`\. Example:

```
SELECT ST_Y(ST_Point(1.5, 2.5))
```

### `ST_YMax(geometry)`<a name="geospatial-functions-list-v2-st-ymax"></a>

Returns the maximum Y coordinate of a geometry in type `double`\. Example:

```
SELECT ST_YMax(ST_Line('linestring(0 2, 2 2)'))
```

### `ST_YMin(geometry)`<a name="geospatial-functions-list-v2-st-ymin"></a>

Returns the minimum Y coordinate of a geometry in type `double`\. Example:

```
SELECT ST_YMin(ST_Line('linestring(0 2, 2 2)'))
```