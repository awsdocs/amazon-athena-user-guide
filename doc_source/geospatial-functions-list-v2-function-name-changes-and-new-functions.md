# Geospatial Function Name Changes and New Functions in Athena engine version 2<a name="geospatial-functions-list-v2-function-name-changes-and-new-functions"></a>

This section lists changes in geospatial function names and geospatial functions that are new in Athena engine version 2\. Currently, Athena engine version 2 is supported in the Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), Europe \(Ireland\), Europe \(London\), Europe \(Paris\), US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), and US West \(Oregon\) Regions\. 

For information about other changes in Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\.

For information about Athena engine versioning, see [Athena Engine Versioning](engine-versions.md)\.

## Geospatial Function Name Changes in Athena engine version 2<a name="geospatial-functions-list-v2-function-name-changes"></a>

The names of the following functions have changed\. In some cases, the input and output types have also changed\. For more information, visit the corresponding links\. 


| Athena engine version 1 Function Name | Athena engine version 2 Function Name | 
| --- | --- | 
| [st\_coordinate\_dimension](geospatial-functions-list-v1.md#st-coordinate-dimension-geometry) | [ST\_CoordDim](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-coordim) | 
| [st\_end\_point](geospatial-functions-list-v1.md#st-end-point-geometry) | [ST\_EndPoint](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-end-point) | 
| [st\_exterior\_ring](geospatial-functions-list-v1.md#st-exterior-ring-geometry) | [ST\_ExteriorRing](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-exteriorring) | 
| [st\_interior\_ring\_number](geospatial-functions-list-v1.md#st-interior-ring-number-geometry) | [ST\_NumInteriorRing](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-numinteriorring) | 
| [st\_geometry\_from\_text](geospatial-functions-list-v1.md#st-geometry-from-text-varchar) | [ST\_GeometryFromText](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geometryfromtext) | 
| [st\_is\_closed](geospatial-functions-list-v1.md#st-is-closed-geometry) | [ST\_IsClosed](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-isclosed) | 
| [st\_is\_empty](geospatial-functions-list-v1.md#st-is-empty-geometry) | [ST\_IsEmpty](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-isempty) | 
| [st\_is\_ring](geospatial-functions-list-v1.md#st-is-ring-geometry) | [ST\_IsRing](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-isring) | 
| [st\_max\_x](geospatial-functions-list-v1.md#st-max-x-geometry) | [ST\_XMax](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-xmax) | 
| [st\_max\_y](geospatial-functions-list-v1.md#st-max-y-geometry) | [ST\_YMax](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-ymax) | 
| [st\_min\_x](geospatial-functions-list-v1.md#st-min-x-geometry) | [ST\_XMin](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-xmin) | 
| [st\_min\_y](geospatial-functions-list-v1.md#st-min-y-geometry) | [ST\_YMin](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-ymin) | 
| [st\_point\_number](geospatial-functions-list-v1.md#st-point-number-geometry) | [ST\_NumPoints](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-numpoints) | 
| [st\_start\_point](geospatial-functions-list-v1.md#st-start-point-geometry) | [ST\_StartPoint](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-startpoint) | 
| [st\_symmetric\_difference](geospatial-functions-list-v1.md#st-symmetric-difference-geometry-geometry) | [ST\_SymDifference](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-symdifference) | 

## New Geospatial Functions in Athena engine version 2<a name="geospatial-functions-list-v2-new-functions"></a>

The following geospatial functions are new in Athena engine version 2\. For more information, visit the corresponding links\.

**Constructor Functions**
+ [ST\_AsBinary](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-asbinary)
+ [ST\_GeomAsLegacyBinary](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geomaslegacybinary)
+ [ST\_GeomFromBinary](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geomfrombinary)
+ [ST\_GeomFromLegacyBinary](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geomfromlegacybinary)
+ [ST\_LineString](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-linestring)
+ [ST\_MultiPoint](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-multipoint)
+ [to\_geometry](geospatial-functions-list-v2.md#geospatial-functions-list-v2-to-geometry)
+ [to\_spherical\_geography](geospatial-functions-list-v2.md#geospatial-functions-list-v2-to-spherical-geography)

**Operation Functions**
+ [geometry\_union](geospatial-functions-list-v2.md#geospatial-functions-list-v2-geometry-union)
+ [ST\_EnvelopeAsPts](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-envelopeaspts)
+ [ST\_Union](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-union)

**Accessor Functions**
+ [geometry\_invalid\_reason](geospatial-functions-list-v2.md#geospatial-functions-list-v2-geometry-invalid-reason)
+ [great\_circle\_distance](geospatial-functions-list-v2.md#geospatial-functions-list-v2-great-circle-distance)
+ [line\_locate\_point](geospatial-functions-list-v2.md#geospatial-functions-list-v2-line-locate-point)
+ [simplify\_geometry](geospatial-functions-list-v2.md#geospatial-functions-list-v2-simplify-geometry)
+ [ST\_ConvexHull](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-convexhull)
+ [ST\_Distance \(spherical geography\)](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-distance-spherical)
+ [ST\_Geometries](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geometries)
+ [ST\_GeometryN](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geometryn)
+ [ST\_GeometryType](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geometrytype)
+ [ST\_InteriorRingN](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-interiorringn)
+ [ST\_InteriorRings](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-interiorrings)
+ [ST\_IsSimple](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-issimple)
+ [ST\_IsValid](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-isvalid)
+ [ST\_NumGeometries](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-numgeometries)
+ [ST\_PointN](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-pointn)
+ [ST\_Points](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-points)

**Aggregation Functions**
+ [convex\_hull\_agg](geospatial-functions-list-v2.md#geospatial-functions-list-v2-convex-hull-agg)
+ [geometry\_union\_agg](geospatial-functions-list-v2.md#geospatial-functions-list-v2-geometry-union-agg)

**Bing Tile Functions**
+ [bing\_tile](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tile)
+ [bing\_tile \(quadkey\)](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tile-quadkey)
+ [bing\_tile\_at](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tile-at)
+ [bing\_tiles\_around](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tiles-around)
+ [bing\_tiles\_around \(radius\)](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tiles-around-radius)
+ [bing\_tile\_coordinates](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tile-coordinates)
+ [bing\_tile\_polygon](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tile-polygon)
+ [bing\_tile\_quadkey](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tile-quadkey-return)
+ [bing\_tile\_zoom\_level](geospatial-functions-list-v2.md#geospatial-functions-list-v2-bing-tile-zoom-level)
+ [geometry\_to\_bing\_tiles](geospatial-functions-list-v2.md#geospatial-functions-list-v2-geometry-to-bing-tiles)