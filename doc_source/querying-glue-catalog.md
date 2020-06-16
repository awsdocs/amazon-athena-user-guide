# Querying AWS Glue Data Catalog<a name="querying-glue-catalog"></a>

Because AWS Glue Data Catalog is used by many AWS services as their central metadata repository, you might want to query Data Catalog metadata\. To do so, you can use SQL queries in Athena\. You can use Athena to query AWS Glue catalog metadata like databases, tables, partitions, and columns\.

**Note**  
You can individual hive [DDL commands](language-reference.md) to extract metadata information for specific databases, tables, views, partitions, and columns from Athena, but the output is in a non\-tabular format\.

To obtain AWS Glue Catalog metadata, you query the `information_schema` database on the Athena backend\. The example queries in this topic show how to use Athena to query AWS Glue Catalog metadata for common use cases\.

**Important**  
 You cannot use `CREATE VIEW` to create a view on the `information_schema` database\. 

**Topics**
+ [Listing Databases and Searching a Specified Database](#querying-glue-catalog-querying-available-databases-including-rdbms)
+ [Listing Tables in a Specified Database and Searching for a Table by Name](#querying-glue-catalog-listing-tables)
+ [Listing Partitions for a Specific Table](#querying-glue-catalog-listing-partitions)
+ [Listing or Searching Columns for a Specified Table or View](#querying-glue-catalog-listing-columns)

## Listing Databases and Searching a Specified Database<a name="querying-glue-catalog-querying-available-databases-including-rdbms"></a>

The examples in this section show how to list the databases in metadata by schema name\.

**Example – Listing Databases**  
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

**Example – Searching a Specified Database**  
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

## Listing Tables in a Specified Database and Searching for a Table by Name<a name="querying-glue-catalog-listing-tables"></a>

To list metadata for tables, you can query by table schema or by table name\.

**Example – Listing Tables by Schema**  
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

**Example – Searching for a Table by Name**  
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

## Listing Partitions for a Specific Table<a name="querying-glue-catalog-listing-partitions"></a>

You can use a metadata query to list the partition numbers and partition values for a specific table\.

**Example – Querying the Partitions for a Table**  
The following example query lists the partitions for the table `CloudTrail_logs_test2`\.  

```
SELECT *
FROM   information_schema.__internal_partitions__
WHERE  table_schema = 'default'
       AND table_name = 'cloudtrail_logs_test2'
ORDER  BY partition_number
```
If the query does not work as expected, use `SHOW PARTITIONS table_name` to extract the partition details for a specified table, as in the following example\.  

```
SHOW PARTITIONS CloudTrail_logs_test2
```
The following table shows sample results\.  


****  

|  | table\_catalog | table\_schema | table\_name | partition\_number | partition\_key | partition\_value | 
| --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | default | CloudTrail\_logs\_test2 | 1 | year | 2018 | 
| 2 | awsdatacatalog | default | CloudTrail\_logs\_test2 | 1 | month | 09 | 
| 3 | awsdatacatalog | default | CloudTrail\_logs\_test2 | 1 | day | 30 | 

## Listing or Searching Columns for a Specified Table or View<a name="querying-glue-catalog-listing-columns"></a>

You can list all columns for a table, all columns for a view, or search for a column by name in a specified database and table\.

**Example – Listing All Columns for a Specified Table**  
The following example query lists all columns for the table `rdspostgresqldb1_public_account`\.  

```
SELECT *
FROM   information_schema.columns
WHERE  table_schema = 'rdspostgresql'
       AND table_name = 'rdspostgresqldb1_public_account'
```
The following table shows sample results\.  


****  

|  | table\_catalog | table\_schema | table\_name | column\_name | ordinal\_position | column\_default | is\_nullable | data\_type | comment | extra\_info | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | password | 1 |  | YES | varchar |  |  | 
| 2 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | user\_id | 2 |  | YES | integer |  |  | 
| 3 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | created\_on | 3 |  | YES | timestamp |  |  | 
| 4 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | last\_login | 4 |  | YES | timestamp |  |  | 
| 5 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | email | 5 |  | YES | varchar |  |  | 
| 6 | awsdatacatalog | rdspostgresql | rdspostgresqldb1\_public\_account | username | 6 |  | YES | varchar |  |  | 

**Example – Listing the Columns for a Specified View**  
The following example query lists all the columns in the `default` database for the view `arrayview`\.  

```
SELECT *
FROM   information_schema.columns
WHERE  table_schema = 'default'
       AND table_name = 'arrayview'
```
The following table shows sample results\.  


****  

|  | table\_catalog | table\_schema | table\_name | column\_name | ordinal\_position | column\_default | is\_nullable | data\_type | comment | extra\_info | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | default | arrayview | searchdate | 1 |  | YES | varchar |  |  | 
| 2 | awsdatacatalog | default | arrayview | sid | 2 |  | YES | varchar |  |  | 
| 3 | awsdatacatalog | default | arrayview | btid | 3 |  | YES | varchar |  |  | 
| 4 | awsdatacatalog | default | arrayview | p | 4 |  | YES | varchar |  |  | 
| 5 | awsdatacatalog | default | arrayview | infantprice | 5 |  | YES | varchar |  |  | 
| 6 | awsdatacatalog | default | arrayview | sump | 6 |  | YES | varchar |  |  | 
| 7 | awsdatacatalog | default | arrayview | journeymaparray | 7 |  | YES | array\(varchar\) |  |  | 

**Example – Searching for a Column by Name in a Specified Database and Table**  
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

|  | table\_catalog | table\_schema | table\_name | column\_name | ordinal\_position | column\_default | is\_nullable | data\_type | comment | extra\_info | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 1 | awsdatacatalog | default | arrayview | sid | 2 |  | YES | varchar |  |  | 