# LazySimpleSerDe for CSV, TSV, and Custom\-Delimited Files<a name="lazy-simple-serde"></a>

Specifying this SerDe is optional\. This is the SerDe for data in CSV, TSV, and custom\-delimited formats that Athena uses by default\. This SerDe is used if you don't specify any SerDe and only specify `ROW FORMAT DELIMITED`\. Use this SerDe if your data does not have values enclosed in quotes\.

## Library Name<a name="library-name"></a>

The Class library name for the LazySimpleSerDe is `org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe`\. For more information, see [LazySimpleSerDe](https://hive.apache.org/javadocs/r2.1.1/api/org/apache/hadoop/hive/serde2/lazy/LazySimpleSerDe.html)\. 

## Examples<a name="examples"></a>

The following examples show how to create tables in Athena from CSV and TSV, using the `LazySimpleSerDe`\. To deserialize custom\-delimited file using this SerDe, specify the delimiters similar to the following examples\.
+  [CSV Example](#csv-example) 
+  [TSV Example](#tsv-example) 

**Note**  
You can query data in regions other than the region where you run Athena\. Standard inter\-region data transfer rates for Amazon S3 apply in addition to standard Athena charges\. To reduce data transfer charges, replace *myregion* in `s3://athena-examples-myregion/path/to/data/` with the region identifier where you run Athena, for example, `s3://athena-examples-us-east-1/path/to/data/`\.

**Note**  
The flight table data comes from [Flights](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time) provided by US Department of Transportation, [Bureau of Transportation Statistics](http://www.transtats.bts.gov/)\. Desaturated from original\.

### CSV Example<a name="csv-example"></a>

Use the `CREATE TABLE` statement to create an Athena table from the underlying data in CSV stored in Amazon S3\.

```
CREATE EXTERNAL TABLE flight_delays_csv (
    yr INT,
    quarter INT,
    month INT,
    dayofmonth INT,
    dayofweek INT,
    flightdate STRING,
    uniquecarrier STRING,
    airlineid INT,
    carrier STRING,
    tailnum STRING,
    flightnum STRING,
    originairportid INT,
    originairportseqid INT,
    origincitymarketid INT,
    origin STRING,
    origincityname STRING,
    originstate STRING,
    originstatefips STRING,
    originstatename STRING,
    originwac INT,
    destairportid INT,
    destairportseqid INT,
    destcitymarketid INT,
    dest STRING,
    destcityname STRING,
    deststate STRING,
    deststatefips STRING,
    deststatename STRING,
    destwac INT,
    crsdeptime STRING,
    deptime STRING,
    depdelay INT,
    depdelayminutes INT,
    depdel15 INT,
    departuredelaygroups INT,
    deptimeblk STRING,
    taxiout INT,
    wheelsoff STRING,
    wheelson STRING,
    taxiin INT,
    crsarrtime INT,
    arrtime STRING,
    arrdelay INT,
    arrdelayminutes INT,
    arrdel15 INT,
    arrivaldelaygroups INT,
    arrtimeblk STRING,
    cancelled INT,
    cancellationcode STRING,
    diverted INT,
    crselapsedtime INT,
    actualelapsedtime INT,
    airtime INT,
    flights INT,
    distance INT,
    distancegroup INT,
    carrierdelay INT,
    weatherdelay INT,
    nasdelay INT,
    securitydelay INT,
    lateaircraftdelay INT,
    firstdeptime STRING,
    totaladdgtime INT,
    longestaddgtime INT,
    divairportlandings INT,
    divreacheddest INT,
    divactualelapsedtime INT,
    divarrdelay INT,
    divdistance INT,
    div1airport STRING,
    div1airportid INT,
    div1airportseqid INT,
    div1wheelson STRING,
    div1totalgtime INT,
    div1longestgtime INT,
    div1wheelsoff STRING,
    div1tailnum STRING,
    div2airport STRING,
    div2airportid INT,
    div2airportseqid INT,
    div2wheelson STRING,
    div2totalgtime INT,
    div2longestgtime INT,
    div2wheelsoff STRING,
    div2tailnum STRING,
    div3airport STRING,
    div3airportid INT,
    div3airportseqid INT,
    div3wheelson STRING,
    div3totalgtime INT,
    div3longestgtime INT,
    div3wheelsoff STRING,
    div3tailnum STRING,
    div4airport STRING,
    div4airportid INT,
    div4airportseqid INT,
    div4wheelson STRING,
    div4totalgtime INT,
    div4longestgtime INT,
    div4wheelsoff STRING,
    div4tailnum STRING,
    div5airport STRING,
    div5airportid INT,
    div5airportseqid INT,
    div5wheelson STRING,
    div5totalgtime INT,
    div5longestgtime INT,
    div5wheelsoff STRING,
    div5tailnum STRING
)
    PARTITIONED BY (year STRING)
    ROW FORMAT DELIMITED
      FIELDS TERMINATED BY ','
      ESCAPED BY '\\'
      LINES TERMINATED BY '\n'
    LOCATION 's3://athena-examples-myregion/flight/csv/';
```

Run `MSCK REPAIR TABLE` to refresh partition metadata each time a new partition is added to this table:

```
MSCK REPAIR TABLE flight_delays_csv;
```

Query the top 10 routes delayed by more than 1 hour:

```
SELECT origin, dest, count(*) as delays
FROM flight_delays_csv
WHERE depdelayminutes > 60
GROUP BY origin, dest
ORDER BY 3 DESC
LIMIT 10;
```

### TSV Example<a name="tsv-example"></a>

This example presumes source data in TSV saved in `s3://mybucket/mytsv/`\. 

Use a `CREATE TABLE` statement to create an Athena table from the TSV data stored in Amazon S3\. Notice that this example does not reference any SerDe class in `ROW FORMAT` because it uses the LazySimpleSerDe, and it can be omitted\. The example specifies SerDe properties for character and line separators, and an escape character:

```
CREATE EXTERNAL TABLE flight_delays_tsv (
 yr INT,
 quarter INT,
 month INT,
 dayofmonth INT,
 dayofweek INT,
 flightdate STRING,
 uniquecarrier STRING,
 airlineid INT,
 carrier STRING,
 tailnum STRING,
 flightnum STRING,
 originairportid INT,
 originairportseqid INT,
 origincitymarketid INT,
 origin STRING,
 origincityname STRING,
 originstate STRING,
 originstatefips STRING,
 originstatename STRING,
 originwac INT,
 destairportid INT,
 destairportseqid INT,
 destcitymarketid INT,
 dest STRING,
 destcityname STRING,
 deststate STRING,
 deststatefips STRING,
 deststatename STRING,
 destwac INT,
 crsdeptime STRING,
 deptime STRING,
 depdelay INT,
 depdelayminutes INT,
 depdel15 INT,
 departuredelaygroups INT,
 deptimeblk STRING,
 taxiout INT,
 wheelsoff STRING,
 wheelson STRING,
 taxiin INT,
 crsarrtime INT,
 arrtime STRING,
 arrdelay INT,
 arrdelayminutes INT,
 arrdel15 INT,
 arrivaldelaygroups INT,
 arrtimeblk STRING,
 cancelled INT,
 cancellationcode STRING,
 diverted INT,
 crselapsedtime INT,
 actualelapsedtime INT,
 airtime INT,
 flights INT,
 distance INT,
 distancegroup INT,
 carrierdelay INT,
 weatherdelay INT,
 nasdelay INT,
 securitydelay INT,
 lateaircraftdelay INT,
 firstdeptime STRING,
 totaladdgtime INT,
 longestaddgtime INT,
 divairportlandings INT,
 divreacheddest INT,
 divactualelapsedtime INT,
 divarrdelay INT,
 divdistance INT,
 div1airport STRING,
 div1airportid INT,
 div1airportseqid INT,
 div1wheelson STRING,
 div1totalgtime INT,
 div1longestgtime INT,
 div1wheelsoff STRING,
 div1tailnum STRING,
 div2airport STRING,
 div2airportid INT,
 div2airportseqid INT,
 div2wheelson STRING,
 div2totalgtime INT,
 div2longestgtime INT,
 div2wheelsoff STRING,
 div2tailnum STRING,
 div3airport STRING,
 div3airportid INT,
 div3airportseqid INT,
 div3wheelson STRING,
 div3totalgtime INT,
 div3longestgtime INT,
 div3wheelsoff STRING,
 div3tailnum STRING,
 div4airport STRING,
 div4airportid INT,
 div4airportseqid INT,
 div4wheelson STRING,
 div4totalgtime INT,
 div4longestgtime INT,
 div4wheelsoff STRING,
 div4tailnum STRING,
 div5airport STRING,
 div5airportid INT,
 div5airportseqid INT,
 div5wheelson STRING,
 div5totalgtime INT,
 div5longestgtime INT,
 div5wheelsoff STRING,
 div5tailnum STRING
)
 PARTITIONED BY (year STRING)
 ROW FORMAT DELIMITED
  FIELDS TERMINATED BY '\t'
  ESCAPED BY '\\'
  LINES TERMINATED BY '\n'
 LOCATION 's3://athena-examples-myregion/flight/tsv/';
```

Run `MSCK REPAIR TABLE` to refresh partition metadata each time a new partition is added to this table:

```
MSCK REPAIR TABLE flight_delays_tsv;
```

Query the top 10 routes delayed by more than 1 hour:

```
SELECT origin, dest, count(*) as delays
FROM flight_delays_tsv
WHERE depdelayminutes > 60
GROUP BY origin, dest
ORDER BY 3 DESC
LIMIT 10;
```

**Note**  
The flight table data comes from [Flights](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time) provided by US Department of Transportation, [Bureau of Transportation Statistics](http://www.transtats.bts.gov/)\. Desaturated from original\.