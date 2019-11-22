# Reserved Keywords<a name="reserved-words"></a>

When you run queries in Athena that include reserved keywords, you must escape them by enclosing them in special characters\. Use the lists in this topic to check which keywords are reserved in Athena\. 

To escape reserved keywords in DDL statements, enclose them in backticks \(`\)\. To escape reserved keywords in SQL `SELECT` statements and in queries on [Views](views.md), enclose them in double quotes \(''\)\.
+  [List of Reserved Keywords in DDL Statements](#list-of-ddl-reserved-words) 
+  [List of Reserved Keywords in SQL SELECT Statements](#list-of-reserved-words-sql-select) 
+  [Examples of Queries with Reserved Keywords](#examples-reserved-words) 

## List of Reserved Keywords in DDL Statements<a name="list-of-ddl-reserved-words"></a>

Athena uses the following list of reserved keywords in its DDL statements\. If you use them without escaping them, Athena issues an error\. To escape them, enclose them in backticks \(`\)\.

You cannot use DDL reserved keywords as identifier names in DDL statements without enclosing them in backticks \(`\)\.

```
ALL, ALTER, AND, ARRAY, AS, AUTHORIZATION, BETWEEN, BIGINT, BINARY, BOOLEAN, BOTH, 
BY, CASE, CAST, CHAR, COLUMN, CONF, CONSTRAINT, COMMIT, CREATE, CROSS, CUBE, 
CURRENT, CURRENT_DATE, CURRENT_TIMESTAMP, CURSOR, DATABASE, DATE, DAYOFWEEK, DECIMAL, 
DELETE, DESCRIBE, DISTINCT, DOUBLE, DROP, ELSE, END, EXCHANGE, EXISTS, EXTENDED, 
EXTERNAL, EXTRACT, FALSE, FETCH, FLOAT, FLOOR, FOLLOWING, FOR, FOREIGN, FROM, FULL, 
FUNCTION, GRANT, GROUP, GROUPING, HAVING, IF, IMPORT, IN, INNER, INSERT, INT, INTEGER,
INTERSECT, INTERVAL, INTO, IS, JOIN, LATERAL, LEFT, LESS, LIKE, LOCAL, MACRO, MAP, MORE, 
NONE, NOT, NULL, NUMERIC, OF, ON, ONLY, OR, ORDER, OUT, OUTER, OVER, PARTIALSCAN, PARTITION, 
PERCENT, PRECEDING, PRECISION, PRESERVE, PRIMARY, PROCEDURE, RANGE, READS, REDUCE, REGEXP,
REFERENCES, REVOKE, RIGHT, RLIKE, ROLLBACK, ROLLUP, ROW, ROWS, SELECT, SET, SMALLINT, START, TABLE, 
TABLESAMPLE, THEN, TIME, TIMESTAMP, TO, TRANSFORM, TRIGGER, TRUE, TRUNCATE, UNBOUNDED, UNION, 
UNIQUEJOIN, UPDATE, USER, USING, UTC_TMESTAMP, VALUES, VARCHAR, VIEWS, WHEN, WHERE, WINDOW, WITH
```

## List of Reserved Keywords in SQL SELECT Statements<a name="list-of-reserved-words-sql-select"></a>

Athena uses the following list of reserved keywords in SQL `SELECT` statements and in queries on views\. 

If you use these keywords as identifiers, you must enclose them in double quotes \("\) in your query statements\.

```
ALTER, AND, AS, BETWEEN, BY, CASE, CAST,
CONSTRAINT, CREATE, CROSS, CUBE, CURRENT_DATE, CURRENT_PATH, 	
CURRENT_TIME, CURRENT_TIMESTAMP, CURRENT_USER, DEALLOCATE, 	
DELETE, DESCRIBE, DISTINCT, DROP, ELSE, END, ESCAPE, EXCEPT, 	
EXECUTE, EXISTS, EXTRACT, FALSE, FIRST, FOR, FROM, FULL, GROUP, 	
GROUPING, HAVING, IN, INNER, INSERT, INTERSECT, INTO, 	
IS, JOIN, LAST, LEFT, LIKE, LOCALTIME, LOCALTIMESTAMP, NATURAL, 
NORMALIZE, NOT, NULL, ON, OR, ORDER, OUTER, PREPARE, 
RECURSIVE, RIGHT, ROLLUP, SELECT, TABLE, THEN, TRUE, 	
UNESCAPE, UNION, UNNEST, USING, VALUES, WHEN, WHERE, WITH
```

## Examples of Queries with Reserved Words<a name="examples-reserved-words"></a>

The query in the following example uses backticks \(`\) to escape the DDL\-related reserved keywords *partition* and *date* that are used for a table name and one of the column names:

```
CREATE EXTERNAL TABLE `partition` (
`date` INT, 
col2 STRING
)
PARTITIONED BY (year STRING)
STORED AS TEXTFILE
LOCATION 's3://test_bucket/test_examples/';
```

The following example queries include a column name containing the DDL\-related reserved keywords in `ALTER TABLE ADD PARTITION` and `ALTER TABLE DROP PARTITION` statements\. The DDL reserved keywords are enclosed in backticks \(`\):

```
ALTER TABLE test_table 
ADD PARTITION (`date` = '2018-05-14')
```

```
ALTER TABLE test_table 
DROP PARTITION (`partition` = 'test_partition_value')
```

The following example query includes a reserved keyword \(end\) as an identifier in a `SELECT` statement\. The keyword is escaped in double quotes: 

```
SELECT * 
FROM TestTable
WHERE "end" != nil;
```

The following example query includes a reserved keyword \(first\) in a `SELECT` statement\. The keyword is escaped in double quotes: 

```
SELECT "itemId"."first" 
FROM testTable 
LIMIT 10;
```
