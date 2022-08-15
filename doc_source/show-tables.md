# SHOW TABLES<a name="show-tables"></a>

Lists all the base tables and views in a database\.

## Synopsis<a name="synopsis"></a>

```
SHOW TABLES [IN database_name] ['regular_expression']
```

## Parameters<a name="parameters"></a>

**\[IN database\_name\]**  
Specifies the `database_name` from which tables will be listed\. If omitted, the database from the current context is assumed\.  
`SHOW TABLES` may fail if `database_name` uses an [unsupported character](tables-databases-columns-names.md) such as a hyphen\. As a workaround, try enclosing the database name in backticks\.

**\['regular\_expression'\]**  
Filters the list of tables to those that match the `regular_expression` you specify\. To indicate any character in `AWSDataCatalog` tables, you can use the `*` or `.*` wildcard expression\. For Apache Hive databases, use the `.*` wildcard expression\. To indicate a choice between characters, use the `|` character\.

## Examples<a name="examples"></a>

**Example – Show all of the tables in the database `sampledb`**  

```
SHOW TABLES IN sampledb
```
Results  

```
alb_logs
cloudfront_logs
elb_logs
flights_2016
flights_parquet
view_2016_flights_dfw
```

**Example – Show the names of all tables in `sampledb` that include the word "flights"**  

```
SHOW TABLES IN sampledb '*flights*'
```
Results  

```
flights_2016
flights_parquet
view_2016_flights_dfw
```

**Example – Show the names of all tables in `sampledb` that end in the word "logs"**  

```
SHOW TABLES IN sampledb '*logs'
```
Results  

```
alb_logs
cloudfront_logs
elb_logs
```