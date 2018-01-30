# Concatenating Arrays<a name="concatenating-arrays"></a>

To concatenate multiple arrays, use the double pipe `||` operator between them\.

```
SELECT ARRAY [4,5] || ARRAY[ ARRAY[1,2], ARRAY[3,4] ] AS items
```

This query returns:

```
+--------------------------+
| items                    |
+--------------------------+
| [[4, 5], [1, 2], [3, 4]] |
+--------------------------+
```

To combine multiple arrays into a single array, use the `concat` function\.

```
WITH
dataset AS (
  SELECT
    ARRAY ['hello', 'amazon', 'athena'] AS words,
    ARRAY ['hi', 'alexa'] AS alexa
)
SELECT concat(words, alexa) AS welcome_msg
FROM dataset
```

This query returns:

```
+------------------------------------+
| welcome_msg                        |
+------------------------------------+
| [hello, amazon, athena, hi, alexa] |
+------------------------------------+
```