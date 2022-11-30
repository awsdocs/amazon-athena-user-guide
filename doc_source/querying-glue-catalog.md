# Querying AWS Glue Data Catalog<a name="querying-glue-catalog"></a>

Because AWS Glue Data Catalog is used by many AWS services as their central metadata repository, you might want to query Data Catalog metadata\. To do so, you can use SQL queries in Athena\. You can use Athena to query AWS Glue catalog metadata like databases, tables, partitions, and columns\.

To obtain AWS Glue Catalog metadata, you query the `information_schema` database on the Athena backend\. The example queries in this topic show how to use Athena to query AWS Glue Catalog metadata for common use cases\.

**Topics**
+ [Considerations and limitations](#querying-glue-catalog-considerations-limitations)
+ [Listing databases and searching a specified database](#querying-glue-catalog-querying-available-databases-including-rdbms)
+ [Listing tables in a specified database and searching for a table by name](#querying-glue-catalog-listing-tables)
+ [Listing partitions for a specific table](#querying-glue-catalog-listing-partitions)
+ [Listing all columns for all tables](#querying-glue-catalog-listing-all-columns-for-all-tables)
+ [Listing or searching columns for a specified table or view](#querying-glue-catalog-listing-columns)

## Considerations and limitations<a name="querying-glue-catalog-considerations-limitations"></a>
+ Instead of querying the `information_schema` database, it is possible to use individual Apache Hive [DDL commands](ddl-reference.md) to extract metadata information for specific databases, tables, views, partitions, and columns from Athena\. However, the output is in a non\-tabular format\.
+ Querying `information_schema` is most performant if you have a small to moderate amount of AWS Glue metadata\. If you have a large amount of metadata, errors can occur\.
+ You cannot use `CREATE VIEW` to create a view on the `information_schema` database\. 

## Listing databases and searching a specified database<a name="querying-glue-catalog-querying-available-databases-including-rdbms"></a>

The examples in this section show how to list the databases in metadata by schema name\.

**Example – Listing databases**  
The following example query lists the databases from the `information_schema.schemata` table\.  

```
SELECT schema_name
FROM   information_schema.schemata
LIMIT  10;
```
The following table shows sample results\.  


****  

|  |  | 
| --- |--- |
| 6 | alb\-databas1 | 
| 7 | alb\_original\_cust | 
| 8 | alblogsdatabase | 
| 9 | athena\_db\_test | 
| 10 | athena\_ddl\_db | 

**Example – Searching a specified database**  
In the following example query, `rdspostgresql` is a sample database\.  

```
SELECT schema_name
FROM   information_schema.schemata
WHERE  schema_name = 'rdspostgresql'
```
The following table shows sample results\.  


****  

|  | schema\_name | 
| --- | --- | 
| 1 | rdspostgresql | 

## Listing tables in a specified database and searching for a table by name<a name="querying-glue-catalog-listing-tables"></a>

To list metadata for tables, you can query by table schema or by table name\.

**Example – Listing tables by schema**  
The following query lists tables that use the `rdspostgresql` table schema\.  

```
SELECT table_schema,
       table_name,
       table_type
FROM   information_schema.tables
WHERE  table_schema = 'rdspostgresql'
```
The following table shows a sample result\.  


****  

|  | table\_schema | table\_name | table\_type | 
| --- | --- | --- | --- | 
| 1 | rdspostgresql | rdspostgresqldb1\_public\_account | BASE TABLE | 

**Example – Searching for a table by name**  
The following query obtains metadata information for the table `athena1`\.  

```
SELECT table_schema,
       table_name,
       table_type
FROM   information_schema.tables
WHERE  table_name = 'athena1'
```
The following table shows a sample result\.  


****  

|  | table\_schema | table\_name | table\_type | 
| --- | --- | --- | --- | 
| 1 | default | athena1 | BASE TABLE | 

## Listing partitions for a specific table<a name="querying-glue-catalog-listing-partitions"></a>

You can use `SHOW PARTITIONS table_name` to list the partitions for a specified table, as in the following example\.

```
SHOW PARTITIONS cloudtrail_logs_test2
```

You can also use a `$partitions` metadata query to list the partition numbers and partition values for a specific table\.

**Example – Querying the partitions for a table using the $partitions syntax**  
The following example query lists the partitions for the table `cloudtrail_logs_test2` using the `$partitions` syntax\.  

```
SELECT * FROM default."cloudtrail_logs_test2$partitions" ORDER BY partition_number
```
The following table shows sample results\.  


****  

|  | table\_catalog | table\_schema | table\_name | Year | Month | Day | 
| --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | default | cloudtrail\_logs\_test2 | 2020 | 08 | 10 | 
| 2 | awsdatacatalog | default | cloudtrail\_logs\_test2 | 2020 | 08 | 11 | 
| 3 | awsdatacatalog | default | cloudtrail\_logs\_test2 | 2020 | 08 | 12 | 

## Listing all columns for all tables<a name="querying-glue-catalog-listing-all-columns-for-all-tables"></a>

You can list all columns for all tables in `AwsDataCatalog` or for all tables in a specific database in `AwsDataCatalog`\.
+ To list all columns for all databases in `AwsDataCatalog`, use the query `SELECT * FROM information_schema.columns`\.
+ To restrict the results to a specific database, use `table_schema='database_name'` in the `WHERE` clause\.

**Example – Listing all columns for all tables in a specific database**  
The following example query lists all columns for all tables in the database `webdata`\.  

```
SELECT * FROM information_schema.columns WHERE table_schema = 'webdata'            
```

## Listing or searching columns for a specified table or view<a name="querying-glue-catalog-listing-columns"></a>

You can list all columns for a table, all columns for a view, or search for a column by name in a specified database and table\.

To list the columns, use a `SELECT *` query\. In the `FROM` clause, specify `information_schema.columns`\. In the `WHERE` clause, use `table_schema='database_name'` to specify the database and `table_name = 'table_name'` to specify the table or view that has the columns that you want to list\.

**Example – Listing all columns for a specified table**  
The following example query lists all columns for the table `rdspostgresqldb1_public_account`\.  

```
SELECT *
FROM   information_schema.columns
WHERE  table_schema = 'rdspostgresql'
       AND table_name = 'rdspostgresqldb1_public_account'
```
The following table shows sample results\.  


****  

|  | table\_catalog | table\_schema | table\_name | column\_name | ordinal\_position | column\_default | is\_nullable | data\_type | Comment | extra\_info | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | password | 1 |  | YES | varchar |  |  | 
| 2 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | user\_id | 2 |  | YES | integer |  |  | 
| 3 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | created\_on | 3 |  | YES | timestamp |  |  | 
| 4 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | last\_login | 4 |  | YES | timestamp |  |  | 
| 5 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | email | 5 |  | YES | varchar |  |  | 
| 6 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | username | 6 |  | YES | varchar |  |  | 

**Example – Listing the columns for a specified view**  
The following example query lists all the columns in the `default` database for the view `arrayview`\.  

```
SELECT *
FROM   information_schema.columns
WHERE  table_schema = 'default'
       AND table_name = 'arrayview'
```
The following table shows sample results\.  


****  

|  | table\_catalog | table\_schema | table\_name | column\_name | ordinal\_position | column\_default | is\_nullable | data\_type | Comment | extra\_info | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | default | arrayview | searchdate | 1 |  | YES | varchar |  |  | 
| 2 | awsdatacatalog | default | arrayview | sid | 2 |  | YES | varchar |  |  | 
| 3 | awsdatacatalog | default | arrayview | btid | 3 |  | YES | varchar |  |  | 
| 4 | awsdatacatalog | default | arrayview | p | 4 |  | YES | varchar |  |  | 
| 5 | awsdatacatalog | default | arrayview | infantprice | 5 |  | YES | varchar |  |  | 
| 6 | awsdatacatalog | default | arrayview | sump | 6 |  | YES | varchar |  |  | 
| 7 | awsdatacatalog | default | arrayview | journeymaparray | 7 |  | YES | array\(varchar\) |  |  | 

**Example – Searching for a column by name in a specified database and table**  
The following example query searches for metadata for the `sid` column in the `arrayview` view of the `default` database\.  

```
SELECT *
FROM   information_schema.columns
WHERE  table_schema = 'default'
       AND table_name = 'arrayview' 
       AND column_name='sid'
```
The following table shows a sample result\.  


****  

|  | table\_catalog | table\_schema | table\_name | column\_name | ordinal\_position | column\_default | is\_nullable | data\_type | Comment | extra\_info | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | default | arrayview | sid | 2 |  | YES | varchar |  |  | 