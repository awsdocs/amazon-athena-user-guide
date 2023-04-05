# Using parameterized queries<a name="querying-with-prepared-statements"></a>

You can use Athena parameterized queries to re\-run the same query with different parameter values at execution time and help prevent SQL injection attacks\. In Athena, parameterized queries can take the form of execution parameters in any DML query or SQL prepared statements\.
+ Queries with execution parameters can be done in a single step and are not workgroup specific\. You place question marks in any DML query for the values that you want to parameterize\. When you run the query, you declare the execution parameter values sequentially\. The declaration of parameters and the assigning of values for the parameters can be done in the same query, but in a decoupled fashion\. Unlike prepared statements, you can select the workgroup when you submit a query with execution parameters\.
+ Prepared statements require two separate SQL statements: `PREPARE` and `EXECUTE`\. First, you define the parameters in the `PREPARE` statement\. Then, you run an `EXECUTE` statement that supplies the values for the parameters that you defined\. Prepared statements are workgroup specific; you cannot run them outside the context of the workgroup to which they belong\.

## Considerations and limitations<a name="querying-with-prepared-statements-considerations-and-limitations"></a>
+ Parameterized queries are supported only in Athena engine version 2 and later versions\. For information about Athena engine versions, see [Athena engine versioning](engine-versions.md)\.
+ Currently, parameterized queries are supported only for `SELECT`, `INSERT INTO`, `CTAS`, and `UNLOAD` statements\.
+ In parameterized queries, parameters are positional and are denoted by `?`\. Parameters are assigned values by their order in the query\. Named parameters are not supported\.
+ Currently, `?` parameters can be placed only in the `WHERE` clause\. Syntax like `SELECT ? FROM table` is not supported\.
+ Question mark parameters cannot be placed in double or single quotes \(that is, `'?'` and `"?"` are not valid syntax\)\.
+ Prepared statements are workgroup specific, and prepared statement names must be unique within the workgroup\.
+ IAM permissions for prepared statements are required\. For more information, see [Allow access to prepared statements](security-iam-athena-prepared-statements.md)\.
+ Queries with execution parameters in the Athena console are limited to a maximum of 25 question marks\.

## Querying using execution parameters<a name="querying-with-prepared-statements-querying-using-execution-parameters"></a>

You can use question mark placeholders in any DML query to create a parameterized query without creating a prepared statement first\. To run these queries, you can use the Athena console, or use the AWS CLI or the AWS SDK and declare the variables in the `execution-parameters` argument\.

### Running queries with execution parameters in the Athena console<a name="querying-with-prepared-statements-running-queries-with-execution-parameters-in-the-athena-console"></a>

When you run a parameterized query that has execution parameters \(question marks\) in the Athena console, you are prompted for the values in the order in which the question marks occur in the query\.

**To run a query that has execution parameters**

1. Enter a query with question mark placeholders in the Athena editor, as in the following example\.

   ```
   SELECT * FROM "my_database"."my_table"
   WHERE year = ? and month= ? and day= ?
   ```

1. Choose **Run\.**

1. In the **Enter parameters** dialog box, enter a value in order for each of the question marks in the query\.  
![\[Enter values for the query parameters in order\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-with-prepared-statements-1.png)

1. When you are finished entering the parameters, choose **Run**\. The editor shows the query results for the parameter values that you entered\.

At this point, you can do one of the following:
+ Enter different parameter values for the same query, and then choose **Run again**\.
+ To clear all of the values that you entered at once, choose **Clear**\.
+ To edit the query directly \(for example, to add or remove question marks\), close the **Enter parameters** dialog box first\.
+ To save the parameterized query for later use, choose **Save** or **Save as**, and then give the query a name\. For more information about using saved queries, see [Using saved queries](saved-queries.md)\.

As a convenience, the **Enter parameters** dialog box remembers the values that you entered previously for the query as long as you use the same tab in the query editor\.

### Running queries with execution parameters using the AWS CLI<a name="querying-with-prepared-statements-running-queries-with-execution-parameters-using-the-aws-cli"></a>

To use the AWS CLI to run queries with execution parameters, use the `start-query-execution` command and provide a parameterized query in the `query-string` argument\. Then, in the `execution-parameters` argument, provide the values for the execution parameters\. The following example illustrates this technique\.

```
aws athena start-query-execution 
--query-string "SELECT * FROM table WHERE x = ? AND y = ?"
--query-execution-context "Database"="default" 
--result-configuration "OutputLocation"="s3://..."
--execution-parameters "1" "2"
```

## Querying with prepared statements<a name="querying-with-prepared-statements-querying"></a>

You can use a prepared statement for repeated execution of the same query with different query parameters\. A prepared statement contains parameter placeholders whose values are supplied at execution time\.

**Note**  
The maximum number of prepared statements in a workgroup is 1000\.

### SQL statements<a name="querying-with-prepared-statements-sql-statements"></a>

You can use the `PREPARE`, `EXECUTE` and `DEALLOCATE PREPARE` SQL statements to run parameterized queries in the Athena console query editor\. 

 
+ To specify parameters where you would normally use literal values, use question marks in the `PREPARE` statement\.
+ To replace the parameters with values when you run the query, use the `USING` clause in the `EXECUTE` statement\.
+ To remove a prepared statement from the prepared statements in a workgroup, use the `DEALLOCATE PREPARE` statement\.

The following sections provide additional detail about each of these statements\.

#### PREPARE<a name="querying-with-prepared-statements-prepare"></a>

Prepares a statement to be run at a later time\. Prepared statements are saved in the current workgroup with the name that you specify\. The statement can include parameters in place of literals to be replaced when the query is run\. Parameters to be replaced by values are denoted by question marks\.

##### Syntax<a name="querying-with-prepared-statements-prepare-syntax"></a>

```
PREPARE statement_name FROM statement
```

The following table describes these parameters\.


****  

| Parameter | Description | 
| --- | --- | 
| statement\_name | The name of the statement to be prepared\. The name must be unique within the workgroup\. | 
| statement | A SELECT, CTAS, or INSERT INTO query\. | 

##### PREPARE examples<a name="querying-with-prepared-statements-prepare-examples"></a>

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

#### EXECUTE<a name="querying-with-prepared-statements-execute"></a>

Runs a prepared statement\. Values for parameters are specified in the `USING` clause\.

##### Syntax<a name="querying-with-prepared-statements-execute-syntax"></a>

```
EXECUTE statement_name [USING value1 [ ,value2, ... ] ]
```

*statement\_name* is the name of the prepared statement\. *value1* and *value2* are the values to be specified for the parameters in the statement\.

##### EXECUTE examples<a name="querying-with-prepared-statements-execute-examples"></a>

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

#### DEALLOCATE PREPARE<a name="querying-with-prepared-statements-deallocate-prepare"></a>

Removes the prepared statement with the specified name from the list of prepared statements in the current workgroup\.

##### Syntax<a name="querying-with-prepared-statements-deallocate-prepare-syntax"></a>

```
DEALLOCATE PREPARE statement_name
```

*statement\_name* is the name of the prepared statement to be removed\.

##### Example<a name="querying-with-prepared-statements-deallocate-prepare-examples"></a>

The following example removes the `my_select1` prepared statement from the current workgroup\.

```
DEALLOCATE PREPARE my_select1
```

### Executing prepared statements without the USING clause in the Athena console<a name="querying-with-prepared-statements-executing-prepared-statements-without-the-using-clause-athena-console"></a>

If you run an existing prepared statement with the syntax `EXECUTE` *prepared\_statement* in the query editor, Athena opens the **Enter parameters** dialog box so that you can enter the values that would normally go in the `USING` clause of the `EXECUTE ... USING` statement\.

**To run a prepared statement using the **Enter parameters** dialog box**

1. In the query editor, instead of using the syntax `EXECUTE prepared_statement USING` *value1*`,` *value2* ` ...`, use the syntax `EXECUTE` *prepared\_statement*\.

1. Choose **Run**\. The **Enter parameters** dialog box appears\.  
![\[Entering parameter values for a prepared statement in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/querying-with-prepared-statements-2.png)

1. Enter the values in order in the **Execution parameters** dialog box\. Because the original text of the query is not visible, you must remember the meaning of each positional parameter or have the prepared statement available for reference\.

1. Choose **Run**\.

### Creating prepared statements using the AWS CLI<a name="querying-with-prepared-statements-creating-prepared-statements-using-the-aws-cli"></a>

To use the AWS CLI to create a prepared statement, you can use one of the following `athena` commands:
+ Use the `create-prepared-statement` command and provide a query statement that has execution parameters\.
+ Use the `start-query-execution` command and provide a query string that uses the `PREPARE` syntax\.

#### Using create\-prepared\-statement<a name="querying-with-prepared-statements-cli-using-create-prepared-statement"></a>

In a `create-prepared-statement` command, define the query text in the `query-statement` argument, as in the following example\.

```
aws athena create-prepared-statement 
--statement-name PreparedStatement1 
--query-statement "SELECT * FROM table WHERE x = ?" 
--work-group athena-engine-v2
```

#### Using start\-query\-execution and the PREPARE syntax<a name="querying-with-prepared-statements-cli-using-start-query-execution-and-the-prepare-syntax"></a>

Use the `start-query-execution` command\. Put the `PREPARE` statement in the `query-string` argument, as in the following example:

```
aws athena start-query-execution 
--query-string "PREPARE PreparedStatement1 FROM SELECT * FROM table WHERE x = ?" 
--query-execution-context '{"Database": "default"}' 
--result-configuration '{"OutputLocation": "s3://..."}'
```

### Executing prepared statements using the AWS CLI<a name="querying-with-prepared-statements-cli-executing-prepared-statements"></a>

To execute a prepared statement with the AWS CLI, you can supply values for the parameters by using one of the following methods:
+ Use the `execution-parameters` argument\.
+ Use the `EXECUTE ... USING` SQL syntax in the `query-string` argument\.

#### Using the execution\-parameters argument<a name="querying-with-prepared-statements-cli-using-the-execution-parameters-argument"></a>

In this approach, you use the `start-query-execution` command and provide the name of an existing prepared statement in the `query-string` argument\. Then, in the `execution-parameters` argument, you provide the values for the execution parameters\. The following example shows this method\.

```
aws athena start-query-execution 
--query-string "Execute PreparedStatement1" 
--query-execution-context "Database"="default" 
--result-configuration "OutputLocation"="s3://..."
--execution-parameters "1" "2"
```

#### Using the EXECUTE \.\.\. USING SQL syntax<a name="querying-with-prepared-statements-cli-using-the-execute-using-sql-syntax"></a>

To run an existing prepared statement using the `EXECUTE ... USING` syntax, you use the `start-query-execution` command and place the both the name of the prepared statement and the parameter values in the `query-string` argument, as in the following example:

```
aws athena start-query-execution 
--query-string "EXECUTE PreparedStatement1 USING 1"
--query-execution-context '{"Database": "default"}' 
--result-configuration '{"OutputLocation": "s3://..."}'
```

### Listing prepared statements<a name="querying-with-prepared-statements-listing"></a>

To list the prepared statements for a specific workgroup, you can use the Athena [list\-prepared\-statements](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/athena/list-prepared-statements.html) AWS CLI command or the [ListPreparedStatements](https://docs.aws.amazon.com/athena/latest/APIReference/API_ListPreparedStatements.html) Athena API action\. The `--work-group` parameter is required\.

```
aws athena list-prepared-statements --work-group primary
```

## See also<a name="querying-with-prepared-statements-see-also"></a>

See the following related posts in the AWS Big Data Blog\.
+ [Improve reusability and security using Amazon Athena parameterized queries](http://aws.amazon.com/blogs/big-data/improve-reusability-and-security-using-amazon-athena-parameterized-queries/) 
+ [Use Amazon Athena parameterized queries to provide data as a service](http://aws.amazon.com/blogs/big-data/use-amazon-athena-parameterized-queries-to-provide-data-as-a-service/) 