# SHOW CREATE TABLE<a name="show-create-table"></a>

Analyzes an existing table named `table_name` to generate the query that created it\.

## Synopsis<a name="synopsis"></a>

```
SHOW CREATE TABLE [db_name.]table_name
```

## Parameters<a name="parameters"></a>

**TABLE \[db\_name\.\]table\_name**  
The `db_name` parameter is optional\. If omitted, the context defaults to the current database\.   
The table name is required\.

## Examples<a name="examples"></a>

```
SHOW CREATE TABLE orderclickstoday;
```

```
SHOW CREATE TABLE `salesdata.orderclickstoday`;
```