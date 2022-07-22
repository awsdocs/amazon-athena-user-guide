# EXECUTE<a name="sql-execute"></a>

Runs a prepared statement with the name `statement_name`\. Parameter values for the question marks in the prepared statement are defined in the `USING` clause in a comma separated list\. To create a prepared statement, use [PREPARE](sql-prepare.md)\.

## Synopsis<a name="sql-execute-synopsis"></a>

```
EXECUTE statement_name [ USING parameter1[, parameter2, ... ] ]
```

## Examples<a name="sql-execute-examples"></a>

The following example prepares and executes a query with no parameters\.

```
PREPARE my_select1 FROM 
SELECT name FROM nation 
EXECUTE my_select1
```

The following example prepares and executes a query with a single parameter\.

```
PREPARE my_select2 FROM 
SELECT * FROM "my_database"."my_table" WHERE year = ? 
EXECUTE my_select2 USING 2012
```

This is equivalent to:

```
SELECT * FROM "my_database"."my_table" WHERE year = 2012
```

The following example prepares and executes a query with two parameters\.

```
PREPARE my_select3 FROM 
SELECT order FROM orders WHERE productid = ? and quantity < ? 
EXECUTE my_select3 USING 346078, 12
```

## See also<a name="sql-execute-see-also"></a>

[Querying with prepared statements](querying-with-prepared-statements.md#querying-with-prepared-statements-querying)

[PREPARE](sql-prepare.md)

[INSERT INTO](insert-into.md)