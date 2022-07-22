# DEALLOCATE PREPARE<a name="sql-deallocate-prepare"></a>

Removes the prepared statement with the specified name from the prepared statements in the current workgroup\.

## Synopsis<a name="sql-deallocate-prepare-synopsis"></a>

```
DEALLOCATE PREPARE statement_name
```

## Examples<a name="sql-deallocate-prepare-examples"></a>

The following example removes the `my_select1` prepared statement from the current workgroup\.

```
DEALLOCATE PREPARE my_select1
```

## See also<a name="sql-deallocate-prepare-see-also"></a>

[Querying with prepared statements](querying-with-prepared-statements.md#querying-with-prepared-statements-querying)

[PREPARE](sql-prepare.md)