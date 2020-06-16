# Names for Tables, Databases, and Columns<a name="tables-databases-columns-names"></a>

Use these tips for naming items in Athena\.

## Table names and table column names in Athena must be lowercase<a name="table-names-and-table-column-names-in-ate-must-be-lowercase"></a>

If you are interacting with Apache Spark, then your table names and table column names must be lowercase\. Athena is case\-insensitive and turns table names and column names to lower case, but Spark requires lowercase table and column names\.

Queries with mixedCase column names, such as `profileURI`, or upper case column names do not work\.

## Special characters<a name="ate-table-database-and-column-names-special-characters"></a>

You may have to use backticks or double quotes to enclose Athena table, view, database, or column names that contain special characters\.

### Names that begin with an underscore<a name="names-that-begin-with-an-underscore"></a>

Use backticks to enclose table, view, or column names that begin with an underscore\. For example:

```
CREATE EXTERNAL TABLE IF NOT EXISTS `_myunderscoretable`(
  `_id` string, `_index` string)
LOCATION 's3://my-athena-data/'
```

### Table, view, or column names that begin with numbers<a name="table-names-that-include-numbers"></a>

In `SELECT`, `CTAS`, or `VIEW` queries, put quotation marks around identifiers like table, view, or column names that start with a digit\. For example:

```
CREATE OR REPLACE VIEW "123view" AS
SELECT "123columnone", "123columntwo"
FROM "234table"
```

### Reserved words<a name="tables-databases-columns-names-reserved-words"></a>

Certain reserved words in Athena must be escaped\. To escape reserved keywords in DDL statements, enclose them in backticks \(`\)\. To escape reserved keywords in SQL `SELECT` statements and in queries on [views](views.md), enclose them in double quotes \(''\)\. 

For more information, see [Reserved Keywords](reserved-words.md)\.