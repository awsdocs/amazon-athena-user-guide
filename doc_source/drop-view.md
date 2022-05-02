# DROP VIEW<a name="drop-view"></a>

Drops \(deletes\) an existing view\. The optional `IF EXISTS` clause causes the error to be suppressed if the view does not exist\.

For more information, see [Working with views](views.md)\.

## Synopsis<a name="synopsis"></a>

```
DROP VIEW [ IF EXISTS ] view_name
```

## Examples<a name="examples"></a>

```
DROP VIEW orders_by_date
```

```
DROP VIEW IF EXISTS orders_by_date
```

See also [CREATE VIEW](create-view.md), [SHOW COLUMNS](show-columns.md), [SHOW CREATE VIEW](show-create-view.md), [SHOW VIEWS](show-views.md), and [DESCRIBE VIEW](describe-view.md)\.