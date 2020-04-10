# DROP TABLE<a name="drop-table"></a>

Removes the metadata table definition for the table named `table_name`\. When you drop an external table, the underlying data remains intact because all tables in Athena are `EXTERNAL`\.

## Synopsis<a name="synopsis"></a>

```
DROP TABLE [IF EXISTS] table_name
```

## Parameters<a name="parameters"></a>

**\[ IF EXISTS \]**  
Causes the error to be suppressed if `table_name` doesn't exist\.

## Examples<a name="examples"></a>

```
DROP TABLE fulfilled_orders
```

```
DROP TABLE IF EXISTS fulfilled_orders
```

When using the Athena console query editor to drop a table that has special characters other than the underscore \(\_\), use backticks, as in the following example\.

```
DROP TABLE `my-athena-database-01.my-athena-table`
```

When using the JDBC connector to drop a table that has special characters, backtick characters are not required\.

```
DROP TABLE my-athena-database-01.my-athena-table
```