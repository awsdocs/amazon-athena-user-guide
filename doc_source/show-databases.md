# SHOW DATABASES<a name="show-databases"></a>

Lists all databases defined in the metastore\. You can use `DATABASES` or `SCHEMAS`\. They mean the same thing\.

## Synopsis<a name="synopsis"></a>

```
SHOW {DATABASES | SCHEMAS} [LIKE 'regular_expression']
```

## Parameters<a name="parameters"></a>

**\[LIKE '*regular\_expression*'\]**  
Filters the list of databases to those that match the `regular_expression` that you specify\. For wildcard character matching, you can use the combination `.*`, which matches any character zero to unlimited times\.

## Examples<a name="examples"></a>

```
SHOW SCHEMAS;
```

```
SHOW DATABASES LIKE '.*analytics';
```