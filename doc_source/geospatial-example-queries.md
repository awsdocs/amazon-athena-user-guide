# Examples: Geospatial queries<a name="geospatial-example-queries"></a>

The examples in this topic create two tables from sample data available on GitHub and query the tables based on the data\. The sample data, which are for illustration purposes only and are not guaranteed to be accurate, are in the following files:
+ **[https://github.com/Esri/gis-tools-for-hadoop/blob/master/samples/data/earthquake-data/earthquakes.csv](https://github.com/Esri/gis-tools-for-hadoop/blob/master/samples/data/earthquake-data/earthquakes.csv)** – Lists earthquakes that occurred in California\. The example `earthquakes` table uses fields from this data\.
+ **[https://github.com/Esri/gis-tools-for-hadoop/blob/master/samples/data/counties-data/california-counties.json](https://github.com/Esri/gis-tools-for-hadoop/blob/master/samples/data/counties-data/california-counties.json)** – Lists county data for the state of California in [ESRI\-compliant GeoJSON format](https://doc.arcgis.com/en/arcgis-online/reference/geojson.htm)\. The data includes many fields such as `AREA`, `PERIMETER`, `STATE`, `COUNTY`, and `NAME`, but the example `counties` table uses only two: `Name` \(string\), and `BoundaryShape` \(binary\)\. 
**Note**  
Athena uses the `com.esri.json.hadoop.EnclosedJsonInputFormat` to convert the JSON data to geospatial binary format\.

The following code example creates a table called `earthquakes`:

```
CREATE external TABLE earthquakes
(
 earthquake_date string,
 latitude double,
 longitude double,
 depth double,
 magnitude double,
 magtype string,
 mbstations string,
 gap string,
 distance string,
 rms string,
 source string,
 eventid string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE LOCATION 's3://my-query-log/csv/';
```

The following code example creates a table called `counties`:

```
CREATE external TABLE IF NOT EXISTS counties
 (
 Name string,
 BoundaryShape binary
 )
ROW FORMAT SERDE 'com.esri.hadoop.hive.serde.JsonSerde'
STORED AS INPUTFORMAT 'com.esri.json.hadoop.EnclosedJsonInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION 's3://my-query-log/json/';
```

The following example query uses the `CROSS JOIN` function on the `counties` and `earthquake` tables\. The example uses `ST_CONTAINS` to query for counties whose boundaries include earthquake locations, which are specified with `ST_POINT`\. The query groups such counties by name, orders them by count, and returns them in descending order\.

**Note**  
Starting in Athena engine version 2, functions like `ST_CONTAINS` no longer support the `VARBINARY` type as an input\. For this reason, the example uses the [`ST_GeomFromLegacyBinary(varbinary)`](geospatial-functions-list-v2.md#geospatial-functions-list-v2-st-geomfromlegacybinary) function to convert the `boundaryshape` binary value into a geometry\. For more information, see [Changes to geospatial functions](engine-versions-reference-0002.md#engine-versions-reference-0002-changes-to-geospatial-functions) in the [Athena engine version 2](engine-versions-reference-0002.md) reference\.

```
SELECT counties.name,
        COUNT(*) cnt
FROM counties
CROSS JOIN earthquakes
WHERE ST_CONTAINS (ST_GeomFromLegacyBinary(counties.boundaryshape), ST_POINT(earthquakes.longitude, earthquakes.latitude))
GROUP BY  counties.name
ORDER BY  cnt DESC
```

This query returns:

```
+------------------------+
| name             | cnt |
+------------------------+
| Kern             | 36  |
+------------------------+
| San Bernardino   | 35  |
+------------------------+
| Imperial         | 28  |
+------------------------+
| Inyo             | 20  |
+------------------------+
| Los Angeles      | 18  |
+------------------------+
| Riverside        | 14  |
+------------------------+
| Monterey         | 14  |
+------------------------+
| Santa Clara      | 12  |
+------------------------+
| San Benito       | 11  |
+------------------------+
| Fresno           | 11  |
+------------------------+
| San Diego        | 7   |
+------------------------+
| Santa Cruz       | 5   |
+------------------------+
| Ventura          | 3   |
+------------------------+
| San Luis Obispo  | 3   |
+------------------------+
| Orange           | 2   |
+------------------------+
| San Mateo        | 1   |
+------------------------+
```

## Additional resources<a name="geospatial-example-queries-additional-resources"></a>

For additional examples of geospatial queries, see the following blog posts:
+ [Querying OpenStreetMap with Amazon Athena](https://aws.amazon.com/blogs/big-data/querying-openstreetmap-with-amazon-athena/)
+ [Visualize over 200 years of global climate data using Amazon Athena and Amazon QuickSight](https://aws.amazon.com/blogs/big-data/visualize-over-200-years-of-global-climate-data-using-amazon-athena-and-amazon-quicksight/)\.