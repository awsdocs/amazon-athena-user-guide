# SHOW TBLPROPERTIES<a name="show-tblproperties"></a>

Lists table properties for the named table\.

## Synopsis<a name="synopsis"></a>

```
SHOW TBLPROPERTIES table_name [('property_name')]
```

## Parameters<a name="parameters"></a>

**\[\('property\_name'\)\]**  
If included, only the value of the property named `property_name` is listed\.

## Examples<a name="examples"></a>

```
SHOW TBLPROPERTIES orders;
```

```
SHOW TBLPROPERTIES orders('comment');
```