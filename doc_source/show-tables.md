# SHOW TABLES<a name="show-tables"></a>

Lists all the base tables and views in a database\.

## Synopsis<a name="synopsis"></a>

```
SHOW TABLES [IN database_name] ['regular_expression']
```

## Parameters<a name="parameters"></a>

**\[IN database\_name\]**  
Specifies the `database_name` from which tables will be listed\. If omitted, the database from the current context is assumed\.

**\['regular\_expression'\]**  
Filters the list of tables to those that match the `regular_expression` you specify\. Only the wildcards `*`, which indicates any character, or `|`, which indicates a choice between characters, can be used\.

## Examples<a name="examples"></a>

```
SHOW TABLES;
```

```
SHOW TABLES IN marketing_analytics 'orders*';
```