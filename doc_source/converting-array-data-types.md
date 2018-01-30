# Converting Array Data Types<a name="converting-array-data-types"></a>

To convert data in arrays to supported data types, use the `CAST` operator, as `CAST(value AS type)`\. Athena supports all of the native Presto data types\.

```
SELECT
   ARRAY [CAST(4 AS VARCHAR), CAST(5 AS VARCHAR)]
AS items
```

This query returns:

```
+-------+
| items |
+-------+
| [4,5] |
+-------+
```

Create two arrays with key\-value pair elements, convert them to JSON, and concatenate, as in this example:

```
SELECT
   ARRAY[CAST(MAP(ARRAY['a1', 'a2', 'a3'], ARRAY[1, 2, 3]) AS JSON)] ||
   ARRAY[CAST(MAP(ARRAY['b1', 'b2', 'b3'], ARRAY[4, 5, 6]) AS JSON)]
AS items
```

This query returns:

```
+--------------------------------------------------+
| items                                            |
+--------------------------------------------------+
| [{"a1":1,"a2":2,"a3":3}, {"b1":4,"b2":5,"b3":6}] |
+--------------------------------------------------+
```