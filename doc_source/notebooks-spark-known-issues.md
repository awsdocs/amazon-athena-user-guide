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