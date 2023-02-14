# ALTER DATABASE SET DBPROPERTIES<a name="alter-database-set-dbproperties"></a>

Creates one or more properties for a database\. The use of `DATABASE` and `SCHEMA` are interchangeable; they mean the same thing\.

## Synopsis<a name="synopsis"></a>

```
ALTER {DATABASE | SCHEMA} database_name
  SET DBPROPERTIES ('property_name'='property_value' [, ...] )
```

## Parameters<a name="parameters"></a>

**SET DBPROPERTIES \('property\_name'='property\_value' \[, \.\.\.\]**  
Specifies a property or properties for the database named `property_name` and establishes the value for each of the properties respectively as `property_value`\. If `property_name` already exists, the old value is overwritten with `property_value`\.

## Examples<a name="examples"></a>

```
ALTER DATABASE jd_datasets
  SET DBPROPERTIES ('creator'='John Doe', 'department'='applied mathematics');
```

```
ALTER SCHEMA jd_datasets
  SET DBPROPERTIES ('creator'='Jane Doe');
```
