# Querying with Prepared Statements<a name="querying-with-prepared-statements"></a>

You can use the Athena parameterized query feature to prepare statements for repeated execution of the same query with different query parameters\. A prepared statement contains parameter placeholders whose values are supplied at execution time\. Prepared statements enable Athena queries to take parameters directly and help to prevent SQL injection attacks\.

## Considerations and Limitations<a name="querying-with-prepared-statements-considerations-and-limitations"></a>
+ Prepared statements are workgroup\-specific, and prepared statement names must be unique within the workgroup\.
+ Parameterized queries are supported only in Athena engine version 2\. For information about Athena engine versions, see [Athena Engine Versioning](engine-versions.md)\.
+ IAM permissions for prepared statements are required\. For more information, see [Allow Access to Prepared Statements](security-iam-athena-prepared-statements.md)\.
+ Currently, parameterized queries are supported only for `SELECT`, `INSERT INTO`, `CTAS`, and `UNLOAD` statements\.

## SQL Statements<a name="querying-with-prepared-statements-sql-statements"></a>

You can use the `PREPARE`, `EXECUTE` and `DEALLOCATE PREPARE` SQL statements to run parameterized queries in the Athena console Query Editor\.
+ To specify parameters where you would normally use literal values, use question marks in the `PREPARE` statement\.
+ To replace the parameters with values when you run the query, use the `USING` clause in the `EXECUTE` statement\.
+ To remove a prepared statement from the list of prepared statements in a workgroup, use the `DEALLOCATE PREPARE` statement\.

The following sections provide additional detail about each of these statements\.

### PREPARE<a name="querying-with-prepared-statements-prepare"></a>

Prepares a statement to be run at a later time\. Prepared statements are saved in the current workgroup with the name that you specify\. The statement can include parameters in place of literals to be replaced when the query is run\. Parameters to be replaced by values are denoted by question marks\.

#### Syntax<a name="querying-with-prepared-statements-prepare-syntax"></a>

```
PREPARE statement_name FROM statement
```

The following table describes these parameters\.


****  

| Parameter | Description | 
| --- | --- | 
| statement\_name | The name of the statement to be prepared\. The name must be unique within the workgroup\. | 
| statement | A SELECT, CTAS, or INSERT INTO query\. | 

#### PREPARE Examples<a name="querying-with-prepared-statements-prepare-examples"></a>

The following examples show the use of the `PREPARE` statement\. Question marks denote the values to be supplied by the `EXECUTE` statement when the query is run\.

```
PREPARE my_select1 FROM
SELECT * FROM nation
```

```
PREPARE my_select2 FROM
SELECT * FROM "my_database"."my_table" WHERE year = ?
```

```
PREPARE my_select3 FROM
SELECT order FROM orders WHERE productid = ? and quantity < ?
```

```
PREPARE my_insert FROM
INSERT INTO cities_usa (city, state)
SELECT city, state
FROM cities_world
WHERE country = ?
```

```
PREPARE my_unload FROM
UNLOAD (SELECT * FROM table1 WHERE productid < ?)
TO 's3://my_output_bucket/'
WITH (format='PARQUET')
```

### EXECUTE<a name="querying-with-prepared-statements-execute"></a>

Runs a prepared statement\. Values for parameters are specified in the `USING` clause\.

#### Syntax<a name="querying-with-prepared-statements-execute-syntax"></a>

```
EXECUTE statement_name [USING value1 [ ,value2, ... ] ]
```

*statement\_name* is the name of the prepared statement\. *value1* and *value2* are the values to be specified for the parameters in the statement\.

#### EXECUTE Examples<a name="querying-with-prepared-statements-execute-examples"></a>

The following example runs the `my_select1` prepared statement, which contains no parameters\.

```
EXECUTE my_select1
```

The following example runs the `my_select2` prepared statement, which contains a single parameter\.

```
EXECUTE my_select2 USING 2012
```

The following example runs the `my_select3` prepared statement, which has two parameters\.

```
EXECUTE my_select3 USING 346078, 12
```

The following example supplies a string value for a parameter in the prepared statement `my_insert`\.

```
EXECUTE my_insert USING 'usa'
```

The following example supplies a numerical value for the `productid` parameter in the prepared statement `my_unload`\.

```
EXECUTE my_unload USING 12
```

### DEALLOCATE PREPARE<a name="querying-with-prepared-statements-deallocate-prepare"></a>

Removes the prepared statement with the specified name from the list of prepared statements in the current workgroup\.

#### Syntax<a name="querying-with-prepared-statements-deallocate-prepare-syntax"></a>

```
DEALLOCATE PREPARE statement_name
```

*statement\_name* is the name of the prepared statement to be removed\.

#### Example<a name="querying-with-prepared-statements-deallocate-prepare-examples"></a>

The following example removes the `my_select1` prepared statement from the current workgroup\.

```
DEALLOCATE PREPARE my_select1
```