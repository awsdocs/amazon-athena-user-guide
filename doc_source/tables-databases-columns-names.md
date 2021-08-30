# Names for Tables, Databases, and Columns<a name="tables-databases-columns-names"></a>

Use these tips for naming items in Athena\.

## Use lower case for table names and table column names in Athena<a name="table-names-and-table-column-names-in-ate-must-be-lowercase"></a>

Athena accepts mixed case in DDL and DML queries, but lower cases the names when it executes the query\. For this reason, avoid using mixed case for table or column names, and do not rely on casing alone in Athena to distinguish such names\. For example, if you use a DDL statement to create a column named `Castle`, the column created will be lowercased to `castle`\. If you then specify the column name in a DML query as `Castle` or `CASTLE`, Athena will lowercase the name for you to run the query, but display the column heading using the casing that you chose in the query\.

## Special characters<a name="ate-table-database-and-column-names-special-characters"></a>

Special characters other than underscore \(\_\) are not supported\. For more information, see the Apache Hive [LanguageManual DDL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL) documentation\.

**Important**  
Although you may succeed in creating table, view, database, or column names that contain special characters other than underscore by enclosing them in backtick \(`\) characters, subsequent DDL or DML queries that reference them can fail\.

### Names that begin with an underscore<a name="names-that-begin-with-an-underscore"></a>

When creating tables, use backticks to enclose table, view, or column names that begin with an underscore\. For example:

```
CREATE EXTERNAL TABLE IF NOT EXISTS `_myunderscoretable`(
  `_id` string, `_index` string)
LOCATION 's3://my-athena-data/'
```

### Table, view, or column names that begin with numbers<a name="table-names-that-include-numbers"></a>

When running `SELECT`, `CTAS`, or `VIEW` queries, put quotation marks around identifiers like table, view, or column names that start with a digit\. For example:

```
CREATE OR REPLACE VIEW "123view" AS
SELECT "123columnone", "123columntwo"
FROM "234table"
```

### Column names and complex types<a name="tables-databases-columns-names-complex-types"></a>

For complex types, only alphanumeric characters, underscore \(`_`\), and period \(`.`\) are allowed in column names\. To create a table and mappings for keys that have restricted characters, you can use a custom DDL statement\. For more information, see the article [Create Tables in Amazon Athena from Nested JSON and Mappings Using JSONSerDe](http://aws.amazon.com/blogs/big-data/create-tables-in-amazon-athena-from-nested-json-and-mappings-using-jsonserde/) in the *AWS Big Data Blog*\.

### Reserved words<a name="tables-databases-columns-names-reserved-words"></a>

Certain reserved words in Athena must be escaped\. To escape reserved keywords in DDL statements, enclose them in backticks \(`\)\. To escape reserved keywords in SQL `SELECT` statements and in queries on [views](views.md), enclose them in double quotes \(''\)\. 

For more information, see [Reserved Keywords](reserved-words.md)\.