# Extracting data from JSON<a name="extracting-data-from-JSON"></a>

You may have source data containing JSON\-encoded strings that you do not necessarily want to deserialize into a table in Athena\. In this case, you can still run SQL operations on this data, using the JSON functions available in Presto\.

Consider this JSON string as an example dataset\.

```
{"name": "Susan Smith",
"org": "engineering",
"projects":
    [
     {"name":"project1", "completed":false},
     {"name":"project2", "completed":true}
    ]
}
```

## Examples: Extracting properties<a name="examples-extracting-properties"></a>

To extract the `name` and `projects` properties from the JSON string, use the `json_extract` function as in the following example\. The `json_extract` function takes the column containing the JSON string, and searches it using a `JSONPath`\-like expression with the dot `.` notation\.

**Note**  
 `JSONPath` performs a simple tree traversal\. It uses the `$` sign to denote the root of the JSON document, followed by a period and an element nested directly under the root, such as `$.name`\.

```
WITH dataset AS (
  SELECT '{"name": "Susan Smith",
           "org": "engineering",
           "projects": [{"name":"project1", "completed":false},
           {"name":"project2", "completed":true}]}'
    AS blob
)
SELECT
  json_extract(blob, '$.name') AS name,
  json_extract(blob, '$.projects') AS projects
FROM dataset
```

The returned value is a JSON\-encoded string, and not a native Athena data type\.

```
+-----------------------------------------------------------------------------------------------+
| name           | projects                                                                     |
+-----------------------------------------------------------------------------------------------+
| "Susan Smith"  | [{"name":"project1","completed":false},{"name":"project2","completed":true}] |
+-----------------------------------------------------------------------------------------------+
```

To extract the scalar value from the JSON string, use the `json_extract_scalar` function\. It is similar to `json_extract`, but returns only scalar values \(Boolean, number, or string\)\.

**Note**  
Do not use the `json_extract_scalar` function on arrays, maps, or structs\.

```
WITH dataset AS (
  SELECT '{"name": "Susan Smith",
           "org": "engineering",
           "projects": [{"name":"project1", "completed":false},{"name":"project2", "completed":true}]}'
    AS blob
)
SELECT
  json_extract_scalar(blob, '$.name') AS name,
  json_extract_scalar(blob, '$.projects') AS projects
FROM dataset
```

This query returns:

```
+---------------------------+
| name           | projects |
+---------------------------+
| Susan Smith    |          |
+---------------------------+
```

To obtain the first element of the `projects` property in the example array, use the `json_array_get` function and specify the index position\.

```
WITH dataset AS (
  SELECT '{"name": "Bob Smith",
           "org": "engineering",
           "projects": [{"name":"project1", "completed":false},{"name":"project2", "completed":true}]}'
    AS blob
)
SELECT json_array_get(json_extract(blob, '$.projects'), 0) AS item
FROM dataset
```

It returns the value at the specified index position in the JSON\-encoded array\.

```
+---------------------------------------+
| item                                  |
+---------------------------------------+
| {"name":"project1","completed":false} |
+---------------------------------------+
```

To return an Athena string type, use the `[]` operator inside a `JSONPath` expression, then Use the `json_extract_scalar` function\. For more information about `[]`, see [Accessing array elements](accessing-array-elements.md)\.

```
WITH dataset AS (
   SELECT '{"name": "Bob Smith",
             "org": "engineering",
             "projects": [{"name":"project1", "completed":false},{"name":"project2", "completed":true}]}'
     AS blob
)
SELECT json_extract_scalar(blob, '$.projects[0].name') AS project_name
FROM dataset
```

It returns this result:

```
+--------------+
| project_name |
+--------------+
| project1     |
+--------------+
```