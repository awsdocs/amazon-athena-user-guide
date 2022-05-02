# SHOW COLUMNS<a name="show-columns"></a>

Shows the column names for a specified table or view\. To view additional column metadata \(such as data type\), see [Listing or searching columns for a specified table or view](querying-glue-catalog.md#querying-glue-catalog-listing-columns)\.

## Synopsis<a name="synopsis"></a>

```
SHOW COLUMNS (FROM|IN) database_name.table_name
```

```
SHOW COLUMNS (FROM|IN) table_name [(FROM|IN) database_name]
```

The `FROM` and `IN` keywords can be used interchangeably\. If *table\_name* or *database\_name* has special characters like hyphens, surround the name with backquotes \(for example, ``my-database`.`my-table``\)\. Do not surround the *table\_name* or *database\_name* with single or double quotes\. Currently, the use of `LIKE` and pattern matching expressions is not supported\.

## Examples<a name="examples"></a>

The following equivalent examples show the columns from the `orders` table in the `customers` database\. The first two examples assume that `customers` is the current database\.

```
SHOW COLUMNS FROM orders
```

```
SHOW COLUMNS IN orders
```

```
SHOW COLUMNS FROM customers.orders
```

```
SHOW COLUMNS IN customers.orders
```

```
SHOW COLUMNS FROM orders FROM customers
```

```
SHOW COLUMNS IN orders IN customers
```