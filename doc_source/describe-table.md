# DESCRIBE TABLE<a name="describe-table"></a>

Shows the list of columns, including partition columns, for the named column\. This allows you to examine the attributes of a complex column\.

## Synopsis<a name="synopsis"></a>

```
DESCRIBE [EXTENDED | FORMATTED] [db_name.]table_name [PARTITION partition_spec] [col_name ( [.field_name] | [.'$elem$'] | [.'$key$'] | [.'$value$'] )]
```

## Parameters<a name="parameters"></a>

**\[EXTENDED \| FORMATTED\]**  
Determines the format of the output\. If you specify `EXTENDED`, all metadata for the table is output in Thrift serialized form\. This is useful primarily for debugging and not for general use\. Use `FORMATTED` or omit the clause to show the metadata in tabular format\.

**\[PARTITION partition\_spec\]**  
Lists the metadata for the partition with `partition_spec` if included\.

**\[col\_name \( \[\.field\_name\] \| \[\.'$elem$'\] \| \[\.'$key$'\] \| \[\.'$value$'\] \)\* \]**  
Specifies the column and attributes to examine\. You can specify `.field_name` for an element of a struct, `'$elem$'` for array element, `'$key$'` for a map key, and `'$value$'` for map value\. You can specify this recursively to further explore the complex column\.

### Examples<a name="examples"></a>

```
DESCRIBE orders;
```