# Examples: Geospatial Queries<a name="geospatial-example-queries"></a>

The following examples create two tables and issue a query against them\.

**Note**  
For additional examples of geospatial queries, see the blog post: [Querying OpenStreetMap with Amazon Athena](https://aws.amazon.com/blogs/big-data/querying-openstreetmap-with-amazon-athena/)\.

These examples rely on two files stored in an Amazon S3 location:
+ An `earthquakes.csv` sample file, which lists earthquakes that occurred in California\. This file has fields that correspond to the fields in the table `earthquakes` in the following example\.
+ A `california-counties.json` file, which lists JSON\-encoded county data in the ESRI\-compliant format, and includes many fields, such as AREA, PERIMETER, STATE, COUNTY, and NAME\. The following example shows the `counties` table from this file with two fields only: `Name` \(string\), and `BoundaryShape` \(binary\)\.

**Note**  
These files contain sample data and are not guaranteed to be accurate\. They are used in the documentation for illustration purposes only and are not included with the product\.

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

The following code example uses the `CROSS JOIN` function for the two tables created earlier\. Additionally, for both tables, it uses `ST_CONTAINS` and asks for counties whose boundaries include a geographical location of the earthquakes, specified with `ST_POINT`\. It then groups such counties by name, orders them by count, and returns them in descending order\.

```
SELECT counties.name,
        COUNT(*) cnt
FROM counties
CROSS JOIN earthquakes
WHERE ST_CONTAINS (counties.boundaryshape, ST_POINT(earthquakes.longitude, earthquakes.latitude))
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