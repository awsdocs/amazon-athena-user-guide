# Using Aggregation Functions with Arrays<a name="arrays-and-aggregation"></a>
+ To add values within an array, use `SUM`, as in the following example\.
+ To aggregate multiple rows within an array, use `array_agg`\. For information, see [Creating Arrays from Subqueries](creating-arrays-from-subqueries.md)\.

```
WITH
dataset AS (
  SELECT ARRAY
  [
    ARRAY[1,2,3,4],
    ARRAY[5,6,7,8],
    ARRAY[9,0]
  ] AS items
),
item AS (
  SELECT i AS array_items
  FROM dataset, UNNEST(items) AS t(i)
)
SELECT array_items, sum(val) AS total
FROM item, UNNEST(array_items) AS t(val)
GROUP BY array_items
```

This query returns the following results\. The order of returned results is not guaranteed\.

```
+----------------------+
| array_items  | total |
+----------------------+
| [1, 2, 3, 4] | 10    |
| [5, 6, 7, 8] | 26    |
| [9, 0]       | 9     |
+----------------------+
```