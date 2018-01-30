# DROP TABLE<a name="drop-table"></a>

Removes the metadata table definition for the table named `table_name`\. When you drop an external table, the underlying data remains intact because all tables in Athena are `EXTERNAL`\.

## Synopsis<a name="synopsis"></a>

```
DROP TABLE [IF EXISTS] table_name [PURGE]
```

## Parameters<a name="parameters"></a>

**\[ IF EXISTS \]**  
Causes the error to be suppressed if `table_name` doesn't exist\.

**\[ PURGE \]**  
Applies to managed tables\. Ignored for external tables\. Specifies that data should be removed permanently rather than being moved to the `.Trash/Current` directory\.

## Examples<a name="examples"></a>

```
DROP TABLE fulfilled_orders;
```

```
DROP TABLE IF EXISTS fulfilled_orders PURGE;
```