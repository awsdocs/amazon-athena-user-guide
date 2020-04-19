# Names for Tables, Databases, and Columns<a name="tables-databases-columns-names"></a>

Use these tips for naming items in Athena\.

## Table names and table column names in Athena must be lowercase<a name="table-names-and-table-column-names-in-ate-must-be-lowercase"></a>

If you are interacting with Apache Spark, then your table names and table column names must be lowercase\. Athena is case\-insensitive and turns table names and column names to lower case, but Spark requires lowercase table and column names\.

Queries with mixedCase column names, such as `profileURI`, or upper case column names do not work\.

## Special characters<a name="ate-table-database-and-column-names-special-characters"></a>

You may have to use backticks to enclose Athena table, view, database, or column names that contain special characters\.

### Names that begin with an underscore<a name="names-that-begin-with-an-underscore"></a>

Use backticks to enclose table, view, or column names that begin with an underscore\. For example:

```
CREATE EXTERNAL TABLE IF NOT EXISTS `_myunderscoretable`(
  `_id` string, `_index` string)
LOCATION 's3://my-athena-data/'
```

## Table or view names that begin with numbers<a name="table-names-that-include-numbers"></a>

Enclose table or view names that begin with numbers in quotation marks\. For example:

```
CREATE OR REPLACE VIEW "123view" AS
SELECT column_name1, column_name2
FROM "234table"
```