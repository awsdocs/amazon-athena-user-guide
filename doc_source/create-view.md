# CREATE VIEW<a name="create-view"></a>

Creates a new view from a specified `SELECT` query\. The view is a logical table that can be referenced by future queries\. Views do not contain any data and do not write data\. Instead, the query specified by the view runs each time you reference the view by another query\. 

The optional `OR REPLACE` clause lets you update the existing view by replacing it\. For more information, see [Creating Views](creating-views.md)\.

## Synopsis<a name="synopsis"></a>

```
CREATE [ OR REPLACE ] VIEW view_name AS query
```

## Examples<a name="examples"></a>

To create a view `test` from the table `orders`, use a query similar to the following:

```
CREATE VIEW test AS
SELECT 
orderkey, 
orderstatus, 
totalprice / 2 AS half
FROM orders
```

To create a view `orders_by_date` from the table `orders`, use the following query:

```
CREATE VIEW orders_by_date AS
SELECT orderdate, sum(totalprice) AS price
FROM orders
GROUP BY orderdate
```

To update an existing view, use an example similar to the following:

```
CREATE OR REPLACE VIEW test AS
SELECT orderkey, orderstatus, totalprice / 4 AS quarter
FROM orders
```

See also [SHOW COLUMNS](show-columns.md), [SHOW CREATE VIEW](show-create-view.md), [DESCRIBE VIEW](describe-view.md), and [DROP VIEW](drop-view.md)\.