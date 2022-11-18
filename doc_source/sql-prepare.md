# PREPARE<a name="sql-prepare"></a>

Creates a SQL statement with the name `statement_name` to be run at a later time\. The statement can include parameters represented by question marks\. To supply values for the parameters and run the prepared statement, use [EXECUTE](sql-execute.md)\.

## Synopsis<a name="sql-prepare-synopsis"></a>

```
PREPARE statement_name FROM statement
```

The following table describes the parameters\.


****  

| Parameter | Description | 
| --- | --- | 
| statement\_name | The name of the statement to be prepared\. The name must be unique within the workgroup\. | 
| statement | A SELECT, CTAS, or INSERT INTO query\. | 

**Note**  
The maximum number of prepared statements in a workgroup is 1000\.

## Examples<a name="sql-prepare-examples"></a>

The following example prepares a select query without parameters\.

```
PREPARE my_select1 FROM 
SELECT * FROM nation
```

The following example prepares a select query that includes parameters\. The values for `productid` and `quantity` will be supplied by the `USING` clause of an `EXECUTE` statement:

```
PREPARE my_select2 FROM 
SELECT order FROM orders WHERE productid = ? and quantity < ?
```

The following example prepares an insert query\.

```
PREPARE my_insert FROM 
INSERT INTO cities_usa (city, state) 
SELECT city, state 
FROM cities_world 
WHERE country = ?
```

## See also<a name="sql-prepare-see-also"></a>

[Querying with prepared statements](querying-with-prepared-statements.md#querying-with-prepared-statements-querying)

[EXECUTE](sql-execute.md)

[DEALLOCATE PREPARE](sql-deallocate-prepare.md)

[INSERT INTO](insert-into.md)