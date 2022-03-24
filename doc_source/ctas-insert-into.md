# Using CTAS and INSERT INTO to Create a Table with More Than 100 Partitions<a name="ctas-insert-into"></a>

You can create up to 100 partitions per query with a `CREATE TABLE AS SELECT` \([CTAS](ctas.md)\) query\. Similarly, you can add a maximum of 100 partitions to a destination table with an [INSERT INTO](https://docs.aws.amazon.com/athena/latest/ug/insert-into.html) statement\. 

If you exceed this limitation, you may receive the error message HIVE\_TOO\_MANY\_OPEN\_PARTITIONS: Exceeded limit of 100 open writers for partitions/buckets\. To work around this limitation, you can use a CTAS statement and a series of `INSERT INTO` statements that create or insert up to 100 partitions each\.

The example in this topic uses a database called `tpch100` whose data resides in the Amazon S3 bucket location s3://*<my\-tpch\-bucket>*/\.

**To use CTAS and INSERT INTO to create a table of more than 100 partitions**

1. Use a `CREATE EXTERNAL TABLE` statement to create a table partitioned on the field that you want\.

   The following example statement partitions the data by the column `l_shipdate`\. The table has 2525 partitions\.

   ```
   CREATE EXTERNAL TABLE `tpch100.lineitem_parq_partitioned`(
     `l_orderkey` int, 
     `l_partkey` int, 
     `l_suppkey` int, 
     `l_linenumber` int, 
     `l_quantity` double, 
     `l_extendedprice` double, 
     `l_discount` double, 
     `l_tax` double, 
     `l_returnflag` string, 
     `l_linestatus` string, 
     `l_commitdate` string, 
     `l_receiptdate` string, 
     `l_shipinstruct` string, 
     `l_comment` string)
   PARTITIONED BY ( 
     `l_shipdate` string)
   ROW FORMAT SERDE 
     'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe' STORED AS INPUTFORMAT 
     'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat' OUTPUTFORMAT 
     'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat' LOCATION   's3://<my-tpch-bucket>/lineitem/'
   ```

1. Run a `SHOW PARTITIONS <table_name>` command like the following to list the partitions\.

   ```
   SHOW PARTITIONS lineitem_parq_partitioned
   ```

   Following are partial sample results\.

   ```
   /*
   l_shipdate=1992-01-02
   l_shipdate=1992-01-03
   l_shipdate=1992-01-04
   l_shipdate=1992-01-05
   l_shipdate=1992-01-06
   
   ...
   
   l_shipdate=1998-11-24
   l_shipdate=1998-11-25
   l_shipdate=1998-11-26
   l_shipdate=1998-11-27
   l_shipdate=1998-11-28
   l_shipdate=1998-11-29
   l_shipdate=1998-11-30
   l_shipdate=1998-12-01
   */
   ```

1. Run a CTAS query to create a partitioned table\. 

   The following example creates a table called `my_lineitem_parq_partitioned` and uses the `WHERE `clause to restrict the `DATE` to earlier than `1992-02-01`\. Because the sample dataset starts with January 1992, only partitions for January 1992 are created\.

   ```
   CREATE table my_lineitem_parq_partitioned
   WITH (partitioned_by = ARRAY['l_shipdate']) AS
   SELECT l_orderkey,
            l_partkey,
            l_suppkey,
            l_linenumber,
            l_quantity,
            l_extendedprice,
            l_discount,
            l_tax,
            l_returnflag,
            l_linestatus,
            l_commitdate,
            l_receiptdate,
            l_shipinstruct,
            l_comment,
            l_shipdate
   FROM tpch100.lineitem_parq_partitioned
   WHERE cast(l_shipdate as timestamp) < DATE ('1992-02-01');
   ```

1. Run the `SHOW PARTITIONS` command to verify that the table contains the partitions that you want\.

   ```
   SHOW PARTITIONS my_lineitem_parq_partitioned;
   ```

   The partitions in the example are from January 1992\.

   ```
   /*
   l_shipdate=1992-01-02
   l_shipdate=1992-01-03
   l_shipdate=1992-01-04
   l_shipdate=1992-01-05
   l_shipdate=1992-01-06
   l_shipdate=1992-01-07
   l_shipdate=1992-01-08
   l_shipdate=1992-01-09
   l_shipdate=1992-01-10
   l_shipdate=1992-01-11
   l_shipdate=1992-01-12
   l_shipdate=1992-01-13
   l_shipdate=1992-01-14
   l_shipdate=1992-01-15
   l_shipdate=1992-01-16
   l_shipdate=1992-01-17
   l_shipdate=1992-01-18
   l_shipdate=1992-01-19
   l_shipdate=1992-01-20
   l_shipdate=1992-01-21
   l_shipdate=1992-01-22
   l_shipdate=1992-01-23
   l_shipdate=1992-01-24
   l_shipdate=1992-01-25
   l_shipdate=1992-01-26
   l_shipdate=1992-01-27
   l_shipdate=1992-01-28
   l_shipdate=1992-01-29
   l_shipdate=1992-01-30
   l_shipdate=1992-01-31
   */
   ```

1. Use an `INSERT INTO` statement to add partitions to the table\. 

   The following example adds partitions for the dates from the month of February 1992\.

   ```
   INSERT INTO my_lineitem_parq_partitioned
   SELECT l_orderkey,
            l_partkey,
            l_suppkey,
            l_linenumber,
            l_quantity,
            l_extendedprice,
            l_discount,
            l_tax,
            l_returnflag,
            l_linestatus,
            l_commitdate,
            l_receiptdate,
            l_shipinstruct,
            l_comment,
            l_shipdate
   FROM tpch100.lineitem_parq_partitioned
   WHERE cast(l_shipdate as timestamp) >= DATE ('1992-02-01')
   AND cast(l_shipdate as timestamp) < DATE ('1992-03-01');
   ```

1. Run `SHOW PARTITIONS` again\.

   ```
   SHOW PARTITIONS my_lineitem_parq_partitioned;
   ```

   The sample table now has partitions from both January and February 1992\.

   ```
   /*
   l_shipdate=1992-01-02
   l_shipdate=1992-01-03
   l_shipdate=1992-01-04
   l_shipdate=1992-01-05
   l_shipdate=1992-01-06
   
   ...
   
   l_shipdate=1992-02-20
   l_shipdate=1992-02-21
   l_shipdate=1992-02-22
   l_shipdate=1992-02-23
   l_shipdate=1992-02-24
   l_shipdate=1992-02-25
   l_shipdate=1992-02-26
   l_shipdate=1992-02-27
   l_shipdate=1992-02-28
   l_shipdate=1992-02-29
   */
   ```

1. Continue using `INSERT INTO` statements that read and add no more than 100 partitions each\. Continue until you reach the number of partitions that you require\.
**Important**  
When setting the `WHERE` condition, be sure that the queries don't overlap\. Otherwise, some partitions might have duplicated data\.