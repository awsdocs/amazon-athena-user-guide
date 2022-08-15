# Sorting arrays<a name="sorting-arrays"></a>

To create a sorted array of unique values from a set of rows, you can use the [array\_sort](https://prestodb.io/docs/0.217/functions/array.html#array_sort) function, as in the following example\.

```
WITH
dataset AS (
  SELECT ARRAY[3,1,2,5,2,3,6,3,4,5] AS items
)
SELECT array_sort(array_agg(distinct i)) AS array_items
FROM dataset
CROSS JOIN UNNEST(items) AS t(i)
```

This query returns:

```
+--------------------+
| array_items        |
+--------------------+
| [1, 2, 3, 4, 5, 6] |
+--------------------+
```

For information about expanding an array into multiple rows, see [Flattening nested arrays](flattening-arrays.md)\.