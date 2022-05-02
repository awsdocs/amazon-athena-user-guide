# Concatenating strings and arrays<a name="concatenating-strings-and-arrays"></a>

## Concatenating strings<a name="concatenating-strings"></a>

To concatenate two strings, you can use the double pipe `||` operator, as in the following example\.

```
SELECT 'This' || ' is' || ' a' || ' test.' AS Concatenated_String
```

This query returns:

```
Concatenated_String
This is a test.
```

You can use the `concat()` function to achieve the same result\.

```
SELECT concat('This', ' is', ' a', ' test.') AS Concatenated_String
```

This query returns:

```
Concatenated_String
This is a test.
```

## Concatenating arrays<a name="concatenating-arrays"></a>

You can use the same techniques to concatenate arrays\.

To concatenate multiple arrays, use the double pipe `||` operator\.

```
SELECT ARRAY [4,5] || ARRAY[ ARRAY[1,2], ARRAY[3,4] ] AS items
```

This query returns:

```
items
[[4, 5], [1, 2], [3, 4]]
```

To combine multiple arrays into a single array, use the double pipe operator or the `concat()` function\.

```
WITH
dataset AS (
  SELECT
    ARRAY ['Hello', 'Amazon', 'Athena'] AS words,
    ARRAY ['Hi', 'Alexa'] AS alexa
)
SELECT concat(words, alexa) AS welcome_msg
FROM dataset
```

This query returns:

```
welcome_msg
[Hello, Amazon, Athena, Hi, Alexa]
```

For more information about `concat()` other string functions, see [String functions and operators](https://prestodb.io/docs/0.172/functions/string.html) in the Presto documentation\.