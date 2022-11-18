# DROP DATABASE<a name="drop-database"></a>

Removes the named database from the catalog\. If the database contains tables, you must either drop the tables before running `DROP DATABASE` or use the `CASCADE` clause\. The use of `DATABASE` and `SCHEMA` are interchangeable\. They mean the same thing\.

## Synopsis<a name="synopsis"></a>

```
DROP {DATABASE | SCHEMA} [IF EXISTS] database_name [RESTRICT | CASCADE]
```

## Parameters<a name="parameters"></a>

**\[IF EXISTS\]**  
Causes the error to be suppressed if `database_name` doesn't exist\.

**\[RESTRICT\|CASCADE\]**  
Determines how tables within `database_name` are regarded during the `DROP` operation\. If you specify `RESTRICT`, the database is not dropped if it contains tables\. This is the default behavior\. Specifying `CASCADE` causes the database and all its tables to be dropped\.

## Examples<a name="examples"></a>

```
DROP DATABASE clickstreams;
```

```
DROP SCHEMA IF EXISTS clickstreams CASCADE;
```

**Note**  
When you try to drop a database whose name has special characters \(for example, `my-database`\), you may receive an error message\. To resolve this issue, try enclosing the database name in back tick \(`\) characters\. For information about naming databases in Athena, see [Names for tables, databases, and columns](tables-databases-columns-names.md)\.