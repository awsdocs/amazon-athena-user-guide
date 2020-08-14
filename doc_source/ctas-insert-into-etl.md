# Using CTAS and INSERT INTO for ETL and Data Analysis<a name="ctas-insert-into-etl"></a>

You can use Create Table as Select \([CTAS](ctas.md)\) and [INSERT INTO](insert-into.md) statements in Athena to extract, transform, and load \(ETL\) data into Amazon S3 for data processing\. This topic shows you how to use these statements to partition and convert a dataset into columnar data format to optimize it for data analysis\.

CTAS statements use standard [SELECT](select.md) queries to create new tables\. You can use a CTAS statement to create a subset of your data for analysis\. In one CTAS statement, you can partition the data, specify compression, and convert the data into a columnar format like Apache Parquet or Apache ORC\. When you run the CTAS query, the tables and partitions that it creates are automatically added to the [AWS Glue Data Catalog](https://aws.amazon.com/glue)\. This makes the new tables and partitions that it creates immediately available for subsequent queries\.

INSERT INTO statements insert new rows into a destination table based on a SELECT query statement that runs on a source table\. You can use INSERT INTO statements to transform and load source table data in CSV format into destination table data using all transforms that CTAS supports\.

## Overview<a name="ctas-insert-into-etl-overview"></a>

In Athena, use a CTAS statement to perform an initial batch conversion of the data\. Then use multiple INSERT INTO statements to make incremental updates to the table created by the CTAS statement\.

**Steps**
+ [Step 1: Create a Table Based on the Original Dataset](#ctas-insert-into-etl-step-1-create-a-table-based-on-the-original-dataset)
+  [Step 2: Use CTAS to Partition, Convert, and Compress the Data](#ctas-insert-into-etl-step-2-use-ctas-to-partition-convert-and-compress-the-data) 
+  [Step 3: Use INSERT INTO to Add Data](#ctas-insert-into-etl-step-3-use-insert-into-to-add-data) 
+  [Step 4: Measure Performance and Cost Differences](#ctas-insert-into-etl-step-4-measure-performance-and-cost-differences) 

## Step 1: Create a Table Based on the Original Dataset<a name="ctas-insert-into-etl-step-1-create-a-table-based-on-the-original-dataset"></a>

The example in this topic uses an Amazon S3 readable subset of the publicly available [NOAA Global Historical Climatology Network Daily \(GHCN\-D\)](https://registry.opendata.aws/noaa-ghcn/) dataset\. The data on Amazon S3 has the following characteristics\.

```
Location: s3://aws-bigdata-blog/artifacts/athena-ctas-insert-into-blog/
Total objects: 41727
Size of CSV dataset: 11.3 GB
Region: us-east-1
```

The original data is stored in Amazon S3 with no partitions\. The data is in CSV format in files like the following\.

```
2019-10-31 13:06:57  413.1 KiB artifacts/athena-ctas-insert-into-blog/2010.csv0000
2019-10-31 13:06:57  412.0 KiB artifacts/athena-ctas-insert-into-blog/2010.csv0001
2019-10-31 13:06:57   34.4 KiB artifacts/athena-ctas-insert-into-blog/2010.csv0002
2019-10-31 13:06:57  412.2 KiB artifacts/athena-ctas-insert-into-blog/2010.csv0100
2019-10-31 13:06:57  412.7 KiB artifacts/athena-ctas-insert-into-blog/2010.csv0101
```

The file sizes in this sample are relatively small\. By merging them into larger files, you can reduce the total number of files, enabling better query performance\. You can use CTAS and INSERT INTO statements to enhance query performance\.

**To create a database and table based on the sample dataset**

1. In the Athena query editor, run the [CREATE DATABASE](create-database.md) command to create a database\. To avoid Amazon S3 cross\-Region data transfer charges, run this and the other queries in this topic in the `us-east-1` Region\.

   ```
   CREATE DATABASE blogdb
   ```

1. Run the following statement to [create a table](create-table.md)\.

   ```
   CREATE EXTERNAL TABLE `blogdb`.`original_csv` (
     `id` string,
     `date` string,
     `element` string,
     `datavalue` bigint,
     `mflag` string,
     `qflag` string,
     `sflag` string,
     `obstime` bigint)
   ROW FORMAT DELIMITED
     FIELDS TERMINATED BY ','
   STORED AS INPUTFORMAT
     'org.apache.hadoop.mapred.TextInputFormat'
   OUTPUTFORMAT
     'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION
     's3://aws-bigdata-blog/artifacts/athena-ctas-insert-into-blog/'
   ```

## Step 2: Use CTAS to Partition, Convert, and Compress the Data<a name="ctas-insert-into-etl-step-2-use-ctas-to-partition-convert-and-compress-the-data"></a>

After you create a table, you can use a single [CTAS](ctas.md) statement to convert the data to Parquet format with Snappy compression and to partition the data by year\.

The table you created in Step 1 has a `date` field with the date formatted as `YYYYMMDD` \(for example, `20100104`\)\. Because the new table will be partitioned on `year`, the sample statement in the following procedure uses the Presto function `substr("date",1,4)` to extract the `year` value from the `date` field\.

**To convert the data to Parquet format with Snappy compression, partitioning by year**
+ Run the following CTAS statement, replacing *your\-bucket* with your Amazon S3 bucket location\.

  ```
  CREATE table new_parquet
  WITH (format='PARQUET',
  parquet_compression='SNAPPY',
  partitioned_by=array['year'],
  external_location = 's3://your-bucket/optimized-data/')
  AS
  SELECT id,
           date,
           element,
           datavalue,
           mflag,
           qflag,
           sflag,
           obstime,
           substr("date",1,4) AS year
  FROM original_csv
  WHERE cast(substr("date",1,4) AS bigint) >= 2015
          AND cast(substr("date",1,4) AS bigint) <= 2019
  ```
**Note**  
In this example, the table that you create includes only the data from 2015 to 2019\. In Step 3, you add new data to this table using the INSERT INTO command\.

When the query completes, use the following procedure to verify the output in the Amazon S3 location that you specified in the CTAS statement\.

**To see the partitions and parquet files created by the CTAS statement**

1. To show the partitions created, run the following AWS CLI command\. Be sure to include the final forward slash \(/\)\.

   ```
   aws s3 ls s3://your-bucket/optimized-data/
   ```

   The output shows the partitions\.

   ```
         PRE year=2015/
         PRE year=2016/
         PRE year=2017/
         PRE year=2018/
         PRE year=2019/
   ```

1. To see the Parquet files, run the following command\. Note that the `|` *head \-5* option, which restricts the output to the first five results, is not available on Windows\.

   ```
   aws s3 ls s3://your-bucket/optimized-data/ --recursive --human-readable | head -5
   ```

   The output resembles the following\.

   ```
   2019-10-31 14:51:05    7.3 MiB optimized-data/year=2015/20191031_215021_00001_3f42d_1be48df2-3154-438b-b61d-8fb23809679d
   2019-10-31 14:51:05    7.0 MiB optimized-data/year=2015/20191031_215021_00001_3f42d_2a57f4e2-ffa0-4be3-9c3f-28b16d86ed5a
   2019-10-31 14:51:05    9.9 MiB optimized-data/year=2015/20191031_215021_00001_3f42d_34381db1-00ca-4092-bd65-ab04e06dc799
   2019-10-31 14:51:05    7.5 MiB optimized-data/year=2015/20191031_215021_00001_3f42d_354a2bc1-345f-4996-9073-096cb863308d
   2019-10-31 14:51:05    6.9 MiB optimized-data/year=2015/20191031_215021_00001_3f42d_42da4cfd-6e21-40a1-8152-0b902da385a1
   ```

## Step 3: Use INSERT INTO to Add Data<a name="ctas-insert-into-etl-step-3-use-insert-into-to-add-data"></a>

In Step 2, you used CTAS to create a table with partitions for the years 2015 to 2019\. However, the original dataset also contains data for the years 2010 to 2014\. Now you add that data using an [INSERT INTO](insert-into.md) statement\.

**To add data to the table using one or more INSERT INTO statements**

1. Run the following INSERT INTO command, specifying the years before 2015 in the WHERE clause\.

   ```
   INSERT INTO new_parquet
   SELECT id,
            date,
            element,
            datavalue,
            mflag,
            qflag,
            sflag,
            obstime,
            substr("date",1,4) AS year
   FROM original_csv
   WHERE cast(substr("date",1,4) AS bigint) < 2015
   ```

1. Run the `aws s3 ls` command again, using the following syntax\.

   ```
   aws s3 ls s3://your-bucket/optimized-data/
   ```

   The output shows the new partitions\.

   ```
         PRE year=2010/
         PRE year=2011/
         PRE year=2012/
         PRE year=2013/
         PRE year=2014/
         PRE year=2015/
         PRE year=2016/
         PRE year=2017/
         PRE year=2018/
         PRE year=2019/
   ```

1. To see the reduction in the size of the dataset obtained by using compression and columnar storage in Parquet format, run the following command\.

   ```
   aws s3 ls s3://your-bucket/optimized-data/ --recursive --human-readable --summarize
   ```

   The following results show that the size of the dataset after parquet with Snappy compression is 1\.2 GB\.

   ```
   ...
   2020-01-22 18:12:02 2.8 MiB optimized-data/year=2019/20200122_181132_00003_nja5r_f0182e6c-38f4-4245-afa2-9f5bfa8d6d8f
   2020-01-22 18:11:59 3.7 MiB optimized-data/year=2019/20200122_181132_00003_nja5r_fd9906b7-06cf-4055-a05b-f050e139946e
   Total Objects: 300
        Total Size: 1.2 GiB
   ```

1. If more CSV data is added to original table, you can add that data to the parquet table by using INSERT INTO statements\. For example, if you had new data for the year 2020, you could run the following INSERT INTO statement\. The statement adds the data and the relevant partition to the `new_parquet` table\.

   ```
   INSERT INTO new_parquet
   SELECT id,
            date,
            element,
            datavalue,
            mflag,
            qflag,
            sflag,
            obstime,
            substr("date",1,4) AS year
   FROM original_csv
   WHERE cast(substr("date",1,4) AS bigint) = 2020
   ```
**Note**  
The INSERT INTO statement supports writing a maximum of 100 partitions to the destination table\. However, to add more than 100 partitions, you can run multiple INSERT INTO statements\. For more information, see [Using CTAS and INSERT INTO to Create a Table with More Than 100 Partitions](ctas-insert-into.md)\.

## Step 4: Measure Performance and Cost Differences<a name="ctas-insert-into-etl-step-4-measure-performance-and-cost-differences"></a>

After you transform the data, you can measure the performance gains and cost savings by running the same queries on the new and old tables and comparing the results\.

**Note**  
For Athena per\-query cost information, see [Amazon Athena pricing](https://aws.amazon.com/athena/pricing)\.

**To measure performance gains and cost differences**

1. Run the following query on the original table\. The query finds the number of distinct IDs for every value of the year\.

   ```
   SELECT substr("date",1,4) as year,
          COUNT(DISTINCT id)
   FROM original_csv
   GROUP BY 1 ORDER BY 1 DESC
   ```

1. Note the time that the query ran and the amount of data scanned\.

1. Run the same query on the new table, noting the query runtime and amount of data scanned\.

   ```
   SELECT year,
     COUNT(DISTINCT id)
   FROM new_parquet
   GROUP BY 1 ORDER BY 1 DESC
   ```

1. Compare the results and calculate the performance and cost difference\. The following sample results show that the test query on the new table was faster and cheaper than the query on the old table\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/ctas-insert-into-etl.html)

1. Run the following sample query on the original table\. The query calculates the average maximum temperature \(Celsius\), average minimum temperature \(Celsius\), and average rainfall \(mm\) for the Earth in 2018\.

   ```
   SELECT element, round(avg(CAST(datavalue AS real)/10),2) AS value
   FROM original_csv
   WHERE element IN ('TMIN', 'TMAX', 'PRCP') AND substr("date",1,4) = '2018'
   GROUP BY 1
   ```

1. Note the time that the query ran and the amount of data scanned\.

1. Run the same query on the new table, noting the query runtime and amount of data scanned\.

   ```
   SELECT element, round(avg(CAST(datavalue AS real)/10),2) AS value
   FROM new_parquet
   WHERE element IN ('TMIN', 'TMAX', 'PRCP') and year = '2018'
   GROUP BY 1
   ```

1. Compare the results and calculate the performance and cost difference\. The following sample results show that the test query on the new table was faster and cheaper than the query on the old table\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/ctas-insert-into-etl.html)

## Summary<a name="ctas-insert-into-etl-summary"></a>

This topic showed you how to perform ETL operations using CTAS and INSERT INTO statements in Athena\. You performed the first set of transformations using a CTAS statement that converted data to the Parquet format with Snappy compression\. The CTAS statement also converted the dataset from non\-partitioned to partitioned\. This reduced its size and lowered the costs of running the queries\. When new data becomes available, you can use an INSERT INTO statement to transform and load the data into the table that you created with the CTAS statement\.