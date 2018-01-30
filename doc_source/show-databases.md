# SHOW DATABASES<a name="show-databases"></a>

Lists all databases defined in the metastore\. You can use `DATABASES` or `SCHEMAS`\. They mean the same thing\.

## Synopsis<a name="synopsis"></a>

```
SHOW {DATABASES | SCHEMAS} [LIKE 'regular_expression']
```

## Parameters<a name="parameters"></a>

**\[LIKE 'regular\_expression'\]**  
Filters the list of databases to those that match the `regular_expression` you specify\. Wildcards can only be `*`, which indicates any character, or `|`, which indicates a choice between characters\.

## Examples<a name="examples"></a>

```
SHOW SCHEMAS;
```

```
SHOW DATABASES LIKE '*analytics';
```