# Names for Tables, Databases, and Columns<a name="tables-databases-columns-names"></a>

Use these tips for naming items in Athena\.

## Table names and table column names in Athena must be lowercase<a name="table-names-and-table-column-names-in-ate-must-be-lowercase"></a>

If you are interacting with Apache Spark, then your table names and table column names must be lowercase\. Athena is case\-insensitive and turns table names and column names to lower case, but Spark requires lowercase table and column names\.

Queries with mixedCase column names, such as `profileURI`, or upper case column names do not work\.

## Athena table, database, and column names allow only underscore special characters<a name="ate-table-database-and-column-names-allow-only-underscore-special-characters"></a>

Athena table, database, and column names cannot contain special characters, other than underscore `(_)`\.

## Names that begin with an underscore<a name="names-that-begin-with-an-underscore"></a>

Use backtics to enclose table or column names that begin with an underscore\. For example:

```
CREATE TABLE `_myUnderScoreTable` (
`_id` string,
`_index`string,
...
```

## Table names that include numbers<a name="table-names-that-include-numbers"></a>

Enclose table names that include numbers in quotation marks\. For example:

```
CREATE TABLE "Table123"
`_id` string,
`_index` string,
...
```