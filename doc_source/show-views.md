# SHOW VIEWS<a name="show-views"></a>

Lists the views in the specified database, or in the current database if you omit the database name\. Use the optional `LIKE` clause with a regular expression to restrict the list of view names\.

Athena returns a list of `STRING` type values where each value is a view name\.

## Synopsis<a name="synopsis"></a>

```
SHOW VIEWS [IN database_name] [LIKE 'regular_expression']
```

### Parameters<a name="parameters"></a>

**\[IN database\_name\]**  
Specifies the `database_name` from which views will be listed\. If omitted, the database from the current context is assumed\.

**\[LIKE 'regular\_expression'\]**  
Filters the list of views to those that match the `regular_expression` you specify\. Only the wild card character `*`, which indicates any character, or `|`, which indicates a choice between characters, can be used\.

## Examples<a name="examples"></a>

```
SHOW VIEWS;
```

```
SHOW VIEWS IN marketing_analytics LIKE 'orders*'
```

See also [SHOW COLUMNS](show-columns.md), [SHOW CREATE VIEW](show-create-view.md), [DESCRIBE VIEW](describe-view.md), and [DROP VIEW](drop-view.md)\.