# Using Arrays to Create Maps<a name="maps"></a>

Maps are key\-value pairs that consist of data types available in Athena\. To create maps, use the `MAP` operator and pass it two arrays: the first is the column \(key\) names, and the second is values\. All values in the arrays must be of the same type\. If any of the map value array elements need to be of different types, you can convert them later\.

## Examples<a name="examples"></a>

This example selects a user from a dataset\. It uses the `MAP` operator and passes it two arrays\. The first array includes values for column names, such as "first", "last", and "age"\. The second array consists of values for each of these columns, such as "Bob", "Smith", "35"\.

```
WITH dataset AS (
  SELECT MAP(
    ARRAY['first', 'last', 'age'],
    ARRAY['Bob', 'Smith', '35']
  ) AS user
)
SELECT user FROM dataset
```

This query returns:

```
+---------------------------------+
| user                            |
+---------------------------------+
| {last=Smith, first=Bob, age=35} |
+---------------------------------+
```

You can retrieve `Map` values by selecting the field name followed by `[key_name]`, as in this example:

```
WITH dataset AS (
 SELECT MAP(
   ARRAY['first', 'last', 'age'],
   ARRAY['Bob', 'Smith', '35']
 ) AS user
)
SELECT user['first'] AS first_name FROM dataset
```

This query returns:

```
+------------+
| first_name |
+------------+
| Bob        |
+------------+
```