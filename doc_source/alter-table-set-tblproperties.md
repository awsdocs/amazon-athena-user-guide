# ALTER TABLE SET TBLPROPERTIES<a name="alter-table-set-tblproperties"></a>

Adds custom metadata properties to a table and sets their assigned values\.

[Managed tables](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-ManagedandExternalTables) are not supported, so setting `'EXTERNAL'='FALSE'` has no effect\.

## Synopsis<a name="synopsis"></a>

```
ALTER TABLE table_name SET TBLPROPERTIES ('property_name' = 'property_value' [ , ... ])
```

## Parameters<a name="parameters"></a>

**SET TBLPROPERTIES \('property\_name' = 'property\_value' \[ , \.\.\. \]\)**  
Specifies the metadata properties to add as `property_name` and the value for each as `property value`\. If `property_name` already exists, its value is reset to `property_value`\.

## Examples<a name="examples"></a>

```
ALTER TABLE orders 
SET TBLPROPERTIES ('notes'="Please don't drop this table.");
```