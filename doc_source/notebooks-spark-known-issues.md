# Known issues in Athena for Spark<a name="notebooks-spark-known-issues"></a>

This page documents some of the known issues in Athena for Apache Spark\.

## Illegal argument exception when creating a table<a name="notebooks-spark-known-issues-illegal-argument-exception"></a>

Although Spark does not allow databases to be created with an empty location property, databases in AWS Glue can have an empty `LOCATION` property if they are created outside of Spark\.

If you create a table and specify a AWS Glue database that has an empty `LOCATION` field, an exception like the following can occur: IllegalArgumentException: Cannot create a path from an empty string\.

For example, the following command throws an exception if the default database in AWS Glue contains an empty `LOCATION` field:

```
spark.sql("create table testTable (firstName STRING)")
```

**Suggested solution A** – Use AWS Glue to add a location to the database that you are using\.

**To add a location to an AWS Glue database**

1. Sign in to the AWS Management Console and open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. In the navigation pane, choose **Databases**\.

1. In the list of databases, choose the database that you want to edit\.

1. On the details page for the database, choose **Edit**\.

1. On the **Update a database** page, for **Location**, enter an Amazon S3 location\.

1. Choose **Update Database**\.

**Suggested solution B** – Use a different AWS Glue database that has an existing, valid location in Amazon S3\. For example, if you have a database named `dbWithLocation`, use the command `spark.sql("use dbWithLocation")` to switch to that database\.

**Suggested solution C** – When you use Spark SQL to create the table, specify a value for `location`, as in the following example\.

```
spark.sql("create table testTable (firstName STRING) 
       location 's3://DOC-EXAMPLE-BUCKET/'").
```

**Suggested solution D** – If you specified a location when you created the table, but the issue still occurs, make sure the Amazon S3 path you provide has a trailing forward slash\. For example, the following command throws an illegal argument exception:

```
spark.sql("create table testTable (firstName STRING) 
       location 's3://DOC-EXAMPLE-BUCKET'")
```

To correct this, add a trailing slash to the location \(for example, `'s3:// DOC-EXAMPLE-BUCKET/'`\)\.

## Database created in a workgroup location<a name="notebooks-spark-known-issues-database-created-in-a-workgroup-location"></a>

If you use a command like `spark.sql('create database db')` to create a database and do not specify a location for the database, Athena creates a subdirectory in your workgroup location and uses that location for the newly created database\.

## Issues with Hive managed tables in the AWS Glue default database<a name="notebooks-spark-known-issues-managed-tables"></a>

If the `Location` property of your default database in AWS Glue is nonempty and specifies a valid location in Amazon S3, and you use Athena for Spark to create a Hive managed table in your AWS Glue default database, data are written to the Amazon S3 location specified in your Athena Spark workgroup instead of to the location specified by the AWS Glue database\.

This issue occurs because of how Apache Hive handles its default database\. Apache Hive creates table data in the Hive warehouse root location, which can be different from the actual default database location\.

When you use Athena for Spark to create a Hive managed table under the default database in AWS Glue, the AWS Glue table metadata can point to two different locations\. This can cause unexpected behavior when you attempt an `INSERT` or `DROP TABLE` operation\.

The steps to reproduce the issue are the following:

1. In Athena for Spark, you use one of the following methods to create or save a Hive managed table:
   + A SQL statement like `CREATE TABLE $tableName`
   + A PySpark command like `df.write.mode("overwrite").saveAsTable($tableName)` that does not specify the `path` option in the Dataframe API\.

   At this point, the AWS Glue console may show an incorrect location in Amazon S3 for the table\.

1. In Athena for Spark, you use the `DROP TABLE $table_name` statement to drop the table that you created\.

1. After you run the `DROP TABLE` statement, you notice that the underlying files in Amazon S3 are still present\.

To resolve this issue, do one of the following:

**Solution A** – Use a different AWS Glue database when you create Hive managed tables\.

**Solution B** – Specify an empty location for the default database in AWS Glue\. Then, create your managed tables in the default database\.

## CSV and JSON file format incompatibility between Athena for Spark and Athena SQL<a name="notebooks-spark-known-issues-csv-and-json-file-format-incompatibility"></a>

Due to a known issue with open source Spark, when you create a table in Athena for Spark on CSV or JSON data, the table might not be readable from Athena SQL, and vice versa\. 

For example, you might create a table in Athena for Spark in one of the following ways: 
+ With the following `USING csv` syntax: 

  ```
  spark.sql('''CREATE EXTERNAL TABLE $tableName ( 
  $colName1 $colType1, 
  $colName2 $colType2, 
  $colName3 $colType3) 
  USING csv 
  PARTITIONED BY ($colName1) 
  LOCATION $s3_location''')
  ```
+  With the following [DataFrame](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html) API syntax: 

  ```
  df.write.format('csv').saveAsTable($table_name)
  ```

Due to the known issue with open source Spark, queries from Athena SQL on the resulting tables might not succeed\. 

**Suggested solution** – Try creating the table in Athena for Spark using Apache Hive syntax\. For more information, see [CREATE HIVEFORMAT TABLE](https://spark.apache.org/docs/3.2.1/sql-ref-syntax-ddl-create-table-hiveformat.html) in the Apache Spark documentation\. 