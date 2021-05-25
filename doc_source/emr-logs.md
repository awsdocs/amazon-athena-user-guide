# Querying Amazon EMR Logs<a name="emr-logs"></a>

Amazon EMR and big data applications that run on Amazon EMR produce log files\. Logs files are written to the master node, and you can also configure Amazon EMR to archive log files to Amazon S3 automatically\. You can use Amazon Athena to query these logs to identify events and trends for applications and clusters\. For more information about the types of log files in Amazon EMR and saving them to Amazon S3, see [View Log Files](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-manage-view-web-log-files.html) in the *Amazon EMR Management Guide*\.

## Creating and Querying a Basic Table Based on Amazon EMR Log Files<a name="emr-create-table"></a>

The following example creates a basic table, `myemrlogs`, based on log files saved to `s3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6/elasticmapreduce/`\. The Amazon S3 location used in the examples below reflects the pattern of the default log location for an EMR cluster created by Amazon Web Services account *123456789012* in Region *us\-west\-2*\. If you use a custom location, the pattern is s3://*PathToEMRLogs*/*ClusterID*\.

For information about creating a partitioned table to potentially improve query performance and reduce data transfer, see [Creating and Querying a Partitioned Table Based on Amazon EMR Logs](#emr-create-table-partitioned)\.

```
CREATE EXTERNAL TABLE `myemrlogs`(
  `data` string COMMENT 'from deserializer')
ROW FORMAT DELIMITED  
FIELDS TERMINATED BY '|'
LINES TERMINATED BY '\n'
STORED AS INPUTFORMAT 
  'org.apache.hadoop.mapred.TextInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
  's3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6'
```

The following example queries can be run on the `myemrlogs` table created by the previous example\.

**Example – Query Step Logs for Occurrences of ERROR, WARN, INFO, EXCEPTION, FATAL, or DEBUG**  

```
SELECT data,
        "$PATH"
FROM "default"."myemrlogs"
WHERE regexp_like("$PATH",'s-86URH188Z6B1')
        AND regexp_like(data, 'ERROR|WARN|INFO|EXCEPTION|FATAL|DEBUG') limit 100;
```

**Example – Query a Specific Instance Log, i\-00b3c0a839ece0a9c, for ERROR, WARN, INFO, EXCEPTION, FATAL, or DEBUG**  

```
SELECT "data",
        "$PATH" AS filepath
FROM "default"."myemrlogs"
WHERE regexp_like("$PATH",'i-00b3c0a839ece0a9c')
        AND regexp_like("$PATH",'state')
        AND regexp_like(data, 'ERROR|WARN|INFO|EXCEPTION|FATAL|DEBUG') limit 100;
```

**Example – Query Presto Application Logs for ERROR, WARN, INFO, EXCEPTION, FATAL, or DEBUG**  

```
SELECT "data",
        "$PATH" AS filepath
FROM "default"."myemrlogs"
WHERE regexp_like("$PATH",'presto')
        AND regexp_like(data, 'ERROR|WARN|INFO|EXCEPTION|FATAL|DEBUG') limit 100;
```

**Example – Query Namenode Application Logs for ERROR, WARN, INFO, EXCEPTION, FATAL, or DEBUG**  

```
SELECT "data",
        "$PATH" AS filepath
FROM "default"."myemrlogs"
WHERE regexp_like("$PATH",'namenode')
        AND regexp_like(data, 'ERROR|WARN|INFO|EXCEPTION|FATAL|DEBUG') limit 100;
```

**Example – Query All Logs by Date and Hour for ERROR, WARN, INFO, EXCEPTION, FATAL, or DEBUG**  

```
SELECT distinct("$PATH") AS filepath
FROM "default"."myemrlogs"
WHERE regexp_like("$PATH",'2019-07-23-10')
        AND regexp_like(data, 'ERROR|WARN|INFO|EXCEPTION|FATAL|DEBUG') limit 100;
```

## Creating and Querying a Partitioned Table Based on Amazon EMR Logs<a name="emr-create-table-partitioned"></a>

These examples use the same log location to create an Athena table, but the table is partitioned, and a partition is then created for each log location\. For more information, see [Partitioning Data](partitions.md)\.

The following query creates the partitioned table named `mypartitionedemrlogs`:

```
CREATE EXTERNAL TABLE `mypartitionedemrlogs`(
  `data` string COMMENT 'from deserializer')
 partitioned by (logtype string)
ROW FORMAT DELIMITED  
FIELDS TERMINATED BY '|'
LINES TERMINATED BY '\n'
STORED AS INPUTFORMAT 
  'org.apache.hadoop.mapred.TextInputFormat' 
OUTPUTFORMAT 
 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION 's3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6'
```

The following query statements then create table partitions based on sub\-directories for different log types that Amazon EMR creates in Amazon S3:

```
ALTER TABLE mypartitionedemrlogs ADD
     PARTITION (logtype='containers')
     LOCATION 's3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6/containers/'
```

```
ALTER TABLE mypartitionedemrlogs ADD
     PARTITION (logtype='hadoop-mapreduce')
     LOCATION 's3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6/hadoop-mapreduce/'
```

```
ALTER TABLE mypartitionedemrlogs ADD
     PARTITION (logtype='hadoop-state-pusher')
     LOCATION 's3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6/hadoop-state-pusher/'
```

```
ALTER TABLE mypartitionedemrlogs ADD
     PARTITION (logtype='node')
     LOCATION 's3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6/node/'
```

```
ALTER TABLE mypartitionedemrlogs ADD
     PARTITION (logtype='steps')
     LOCATION 's3://aws-logs-123456789012-us-west-2/elasticmapreduce/j-2ABCDE34F5GH6/steps/'
```

After you create the partitions, you can run a `SHOW PARTITIONS` query on the table to confirm:

```
SHOW PARTITIONS mypartitionedemrlogs;
```

The following examples demonstrate queries for specific log entries use the table and partitions created by the examples above\.

**Example – Querying Application application\_1561661818238\_0002 Logs in the Containers Partition for ERROR or WARN**  

```
SELECT data,
        "$PATH"
FROM "default"."mypartitionedemrlogs"
WHERE logtype='containers'
        AND regexp_like("$PATH",'application_1561661818238_0002')
        AND regexp_like(data, 'ERROR|WARN') limit 100;
```

**Example – Querying the Hadoop\-Mapreduce Partition for Job job\_1561661818238\_0004 and Failed Reduces**  

```
SELECT data,
        "$PATH"
FROM "default"."mypartitionedemrlogs"
WHERE logtype='hadoop-mapreduce'
        AND regexp_like(data,'job_1561661818238_0004|Failed Reduces') limit 100;
```

**Example – Querying Hive Logs in the Node Partition for Query ID 056e0609\-33e1\-4611\-956c\-7a31b42d2663**  

```
SELECT data,
        "$PATH"
FROM "default"."mypartitionedemrlogs"
WHERE logtype='node'
        AND regexp_like("$PATH",'hive')
        AND regexp_like(data,'056e0609-33e1-4611-956c-7a31b42d2663') limit 100;
```

**Example – Querying Resourcemanager Logs in the Node Partition for Application 1567660019320\_0001\_01\_000001**  

```
SELECT data,
        "$PATH"
FROM "default"."mypartitionedemrlogs"
WHERE logtype='node'
        AND regexp_like(data,'resourcemanager')
        AND regexp_like(data,'1567660019320_0001_01_000001') limit 100
```