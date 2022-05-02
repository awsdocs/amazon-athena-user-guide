# Converting arrays to strings<a name="converting-arrays-to-strings"></a>

To convert an array into a single string, use the `array_join` function\. The following standalone example creates a table called `dataset` that contains an aliased array called `words`\. The query uses `array_join` to join the array elements in `words`, separate them with spaces, and return the resulting string in an aliased column called `welcome_msg`\.

```
WITH
dataset AS (
  SELECT ARRAY ['hello', 'amazon', 'athena'] AS words
)
SELECT array_join(words, ' ') AS welcome_msg
FROM dataset
```

This query returns:

```
+---------------------+
| welcome_msg         |
+---------------------+
| hello amazon athena |
+---------------------+
```