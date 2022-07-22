# Filtering arrays<a name="filtering-arrays"></a>

Create an array from a collection of rows if they match the filter criteria\.

```
WITH
dataset AS (
  SELECT ARRAY[1,2,3,4,5] AS items
)
SELECT array_agg(i) AS array_items
FROM dataset
CROSS JOIN UNNEST(items) AS t(i)
WHERE i > 3
```

This query returns:

```
+-------------+
| array_items |
+-------------+
| [4, 5]      |
+-------------+
```

Filter an array based on whether one of its elements contain a specific value, such as 2, as in this example:

```
WITH
dataset AS (
  SELECT ARRAY
  [
    ARRAY[1,2,3,4],
    ARRAY[5,6,7,8],
    ARRAY[9,0]
  ] AS items
)
SELECT i AS array_items FROM dataset
CROSS JOIN UNNEST(items) AS t(i)
WHERE contains(i, 2)
```

This query returns:

```
+--------------+
| array_items  |
+--------------+
| [1, 2, 3, 4] |
+--------------+
```

## The `filter` function<a name="filtering-arrays-filter-function"></a>

```
 filter(ARRAY [list_of_values], boolean_function)
```

You can use the `filter` function on an `ARRAY` expression to create a new array that is the subset of the items in the *list\_of\_values* for which *boolean\_function* is true\. The `filter` function can be useful in cases in which you cannot use the *UNNEST* function\.

The following example filters for values greater than zero in the array `[1,0,5,-1]`\.

```
SELECT filter(ARRAY [1,0,5,-1], x -> x>0)
```

**Results**  
`[1,5]`

The following example filters for the non\-null values in the array `[-1, NULL, 10, NULL]`\.

```
SELECT filter(ARRAY [-1, NULL, 10, NULL], q -> q IS NOT NULL)
```

**Results**  
`[-1,10]`