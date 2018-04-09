# Accessing Array Elements<a name="accessing-array-elements"></a>

To access array elements, use the `[]` operator, with 1 specifying the first element, 2 specifying the second element, and so on, as in this example:

```
WITH dataset AS (
SELECT
   ARRAY[CAST(MAP(ARRAY['a1', 'a2', 'a3'], ARRAY[1, 2, 3]) AS JSON)] ||
   ARRAY[CAST(MAP(ARRAY['b1', 'b2', 'b3'], ARRAY[4, 5, 6]) AS JSON)]
AS items )
SELECT items[1] AS item FROM dataset
```

This query returns:

```
+------------------------+
| item                   |
+------------------------+
| {"a1":1,"a2":2,"a3":3} |
+------------------------+
```

To access the elements of an array at a given position \(known as the index position\), use the `element_at()` function and specify the array name and the index position:
+ If the index is greater than 0, `element_at()` returns the element that you specify, counting from the beginning to the end of the array\. It behaves as the `[]` operator\.
+ If the index is less than 0, `element_at()` returns the element counting from the end to the beginning of the array\.

The following query creates an array `words`, and selects the first element `hello` from it as the `first_word`, the second element `amazon` \(counting from the end of the array\) as the `middle_word`, and the third element `athena`, as the `last_word`\.

```
WITH dataset AS (
  SELECT ARRAY ['hello', 'amazon', 'athena'] AS words
)
SELECT
  element_at(words, 1) AS first_word,
  element_at(words, -2) AS middle_word,
  element_at(words, cardinality(words)) AS last_word
FROM dataset
```

This query returns:

```
+----------------------------------------+
| first_word  | middle_word | last_word  |
+----------------------------------------+
| hello       | amazon      | athena     |
+----------------------------------------+
```