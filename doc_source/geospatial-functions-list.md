# Supported geospatial functions<a name="geospatial-functions-list"></a>

Geospatial functions in Athena have these characteristics:
+ The functions follow the general principles of [Spatial query](https://en.wikipedia.org/wiki/Spatial_query)\.
+ The functions are implemented as a Presto plugin that uses the ESRI Java Geometry Library\. This library has an Apache 2 license\.
+ The functions rely on the [ESRI geometry API](https://esri.github.io/geometry-api-java/javadoc/index.html?overview-summary.html)\.
+ Not all of the ESRI\-supported functions are available in Athena\. The following topics list only the ESRI geospatial functions that are supported in their respective Athena engine versions\. For information about Athena engine versions, see [Athena engine versioning](engine-versions.md)\. 

The geospatial functions that are available in Athena depend on the engine version that you use\. For a list of function name changes and new functions in Athena engine version 2, see [Geospatial function name changes and new functions in Athena engine version 2](geospatial-functions-list-v2-function-name-changes-and-new-functions.md)\. For information about Athena engine versioning, see [Athena engine versioning](engine-versions.md)\.

**Topics**
+ [Geospatial functions in Athena engine version 2](geospatial-functions-list-v2.md)