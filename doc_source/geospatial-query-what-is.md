# What is a Geospatial Query?<a name="geospatial-query-what-is"></a>

Geospatial queries are specialized types of SQL queries supported in Athena\. They differ from non\-spatial SQL queries in the following ways:
+ Using the following specialized geometry data types: `point`, `line`, `multiline`, `polygon`, and `multipolygon`\.
+ Expressing relationships between geometry data types, such as `distance`, `equals`, `crosses`, `touches`, `overlaps`, `disjoint`, and others\.

Using geospatial queries in Athena, you can run these and other similar operations:
+ Find the distance between two points\.
+ Check whether one area \(polygon\) contains another\.
+ Check whether one line crosses or touches another line or polygon\.

For example, to obtain a `point` geometry data type from values of type `double` for the geographic coordinates of Mount Rainier in Athena, use the `ST_Point (longitude, latitude)` geospatial function, as in the following example\. 

```
ST_Point(-121.7602, 46.8527)
```