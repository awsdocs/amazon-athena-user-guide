# Querying arrays with complex types and nested structures<a name="rows-and-structs"></a>

Your source data often contains arrays with complex data types and nested structures\. Examples in this section show how to change element's data type, locate elements within arrays, and find keywords using Athena queries\.
+ [Creating a `ROW`](#creating-row)
+ [Changing field names in arrays using `CAST` ](#changing-row-arrays-with-cast)
+ [Filtering arrays using the `.` notation](#filtering-with-dot)
+ [Filtering arrays with nested values](#filtering-nested-with-dot)
+ [Filtering arrays using `UNNEST` ](#filtering-with-unnest)
+ [Finding keywords in arrays using `regexp_like`](#filtering-with-regexp)

## Creating a `ROW`<a name="creating-row"></a>

**Note**  
The examples in this section use `ROW` as a means to create sample data to work with\. When you query tables within Athena, you do not need to create `ROW` data types, as they are already created from your data source\. When you use `CREATE_TABLE`, Athena defines a `STRUCT` in it, populates it with data, and creates the `ROW` data type for you, for each row in the dataset\. The underlying `ROW` data type consists of named fields of any supported SQL data types\.

```
WITH dataset AS (
 SELECT
   ROW('Bob', 38) AS users
 )
SELECT * FROM dataset
```

This query returns:

```
+-------------------------+
| users                   |
+-------------------------+
| {field0=Bob, field1=38} |
+-------------------------+
```

## Changing field names in arrays using `CAST`<a name="changing-row-arrays-with-cast"></a>

To change the field name in an array that contains `ROW` values, you can `CAST` the `ROW` declaration:

```
WITH dataset AS (
  SELECT
    CAST(
      ROW('Bob', 38) AS ROW(name VARCHAR, age INTEGER)
    ) AS users
)
SELECT * FROM dataset
```

This query returns:

```
+--------------------+
| users              |
+--------------------+
| {NAME=Bob, AGE=38} |
+--------------------+
```

**Note**  
In the example above, you declare `name` as a `VARCHAR` because this is its type in Presto\. If you declare this `STRUCT` inside a `CREATE TABLE` statement, use `String` type because Hive defines this data type as `String`\.

## Filtering arrays using the `.` notation<a name="filtering-with-dot"></a>

In the following example, select the `accountId` field from the `userIdentity` column of a AWS CloudTrail logs table by using the dot `.` notation\. For more information, see [Querying AWS CloudTrail Logs](cloudtrail-logs.md)\.

```
SELECT
  CAST(useridentity.accountid AS bigint) as newid
FROM cloudtrail_logs
LIMIT 2;
```

This query returns:

```
+--------------+
| newid        |
+--------------+
| 112233445566 |
+--------------+
| 998877665544 |
+--------------+
```

To query an array of values, issue this query:

```
WITH dataset AS (
  SELECT ARRAY[
    CAST(ROW('Bob', 38) AS ROW(name VARCHAR, age INTEGER)),
    CAST(ROW('Alice', 35) AS ROW(name VARCHAR, age INTEGER)),
    CAST(ROW('Jane', 27) AS ROW(name VARCHAR, age INTEGER))
  ] AS users
)
SELECT * FROM dataset
```

It returns this result:

```
+-----------------------------------------------------------------+
| users                                                           |
+-----------------------------------------------------------------+
| [{NAME=Bob, AGE=38}, {NAME=Alice, AGE=35}, {NAME=Jane, AGE=27}] |
+-----------------------------------------------------------------+
```

## Filtering arrays with nested values<a name="filtering-nested-with-dot"></a>

Large arrays often contain nested structures, and you need to be able to filter, or search, for values within them\.

To define a dataset for an array of values that includes a nested `BOOLEAN` value, issue this query:

```
WITH dataset AS (
  SELECT
    CAST(
      ROW('aws.amazon.com', ROW(true)) AS ROW(hostname VARCHAR, flaggedActivity ROW(isNew BOOLEAN))
    ) AS sites
)
SELECT * FROM dataset
```

It returns this result:

```
+----------------------------------------------------------+
| sites                                                    |
+----------------------------------------------------------+
| {HOSTNAME=aws.amazon.com, FLAGGEDACTIVITY={ISNEW=true}}  |
+----------------------------------------------------------+
```

Next, to filter and access the `BOOLEAN` value of that element, continue to use the dot `.` notation\.

```
WITH dataset AS (
  SELECT
    CAST(
      ROW('aws.amazon.com', ROW(true)) AS ROW(hostname VARCHAR, flaggedActivity ROW(isNew BOOLEAN))
    ) AS sites
)
SELECT sites.hostname, sites.flaggedactivity.isnew
FROM dataset
```

This query selects the nested fields and returns this result:

```
+------------------------+
| hostname       | isnew |
+------------------------+
| aws.amazon.com | true  |
+------------------------+
```

## Filtering arrays using `UNNEST`<a name="filtering-with-unnest"></a>

To filter an array that includes a nested structure by one of its child elements, issue a query with an `UNNEST` operator\. For more information about `UNNEST`, see [Flattening Nested Arrays](flattening-arrays.md)\.

For example, this query finds host names of sites in the dataset\.

```
WITH dataset AS (
  SELECT ARRAY[
    CAST(
      ROW('aws.amazon.com', ROW(true)) AS ROW(hostname VARCHAR, flaggedActivity ROW(isNew BOOLEAN))
    ),
    CAST(
      ROW('news.cnn.com', ROW(false)) AS ROW(hostname VARCHAR, flaggedActivity ROW(isNew BOOLEAN))
    ),
    CAST(
      ROW('netflix.com', ROW(false)) AS ROW(hostname VARCHAR, flaggedActivity ROW(isNew BOOLEAN))
    )
  ] as items
)
SELECT sites.hostname, sites.flaggedActivity.isNew
FROM dataset, UNNEST(items) t(sites)
WHERE sites.flaggedActivity.isNew = true
```

It returns:

```
+------------------------+
| hostname       | isnew |
+------------------------+
| aws.amazon.com | true  |
+------------------------+
```

## Finding keywords in arrays using `regexp_like`<a name="filtering-with-regexp"></a>

The following examples illustrate how to search a dataset for a keyword within an element inside an array, using the [regexp\_like](https://prestodb.io/docs/0.172/functions/regexp.html) function\. It takes as an input a regular expression pattern to evaluate, or a list of terms separated by a pipe \(\|\), evaluates the pattern, and determines if the specified string contains it\.

The regular expression pattern needs to be contained within the string, and does not have to match it\. To match the entire string, enclose the pattern with ^ at the beginning of it, and $ at the end, such as `'^pattern$'`\.

Consider an array of sites containing their host name, and a `flaggedActivity` element\. This element includes an `ARRAY`, containing several `MAP` elements, each listing different popular keywords and their popularity count\. Assume you want to find a particular keyword inside a `MAP` in this array\.

To search this dataset for sites with a specific keyword, we use `regexp_like` instead of the similar SQL `LIKE` operator, because searching for a large number of keywords is more efficient with `regexp_like`\.

**Example 1: Using `regexp_like`**  
The query in this example uses the `regexp_like` function to search for terms `'politics|bigdata'`, found in values within arrays:  

```
WITH dataset AS (
  SELECT ARRAY[
    CAST(
      ROW('aws.amazon.com', ROW(ARRAY[
          MAP(ARRAY['term', 'count'], ARRAY['bigdata', '10']),
          MAP(ARRAY['term', 'count'], ARRAY['serverless', '50']),
          MAP(ARRAY['term', 'count'], ARRAY['analytics', '82']),
          MAP(ARRAY['term', 'count'], ARRAY['iot', '74'])
      ])
      ) AS ROW(hostname VARCHAR, flaggedActivity ROW(flags ARRAY(MAP(VARCHAR, VARCHAR)) ))
   ),
   CAST(
     ROW('news.cnn.com', ROW(ARRAY[
       MAP(ARRAY['term', 'count'], ARRAY['politics', '241']),
       MAP(ARRAY['term', 'count'], ARRAY['technology', '211']),
       MAP(ARRAY['term', 'count'], ARRAY['serverless', '25']),
       MAP(ARRAY['term', 'count'], ARRAY['iot', '170'])
     ])
     ) AS ROW(hostname VARCHAR, flaggedActivity ROW(flags ARRAY(MAP(VARCHAR, VARCHAR)) ))
   ),
   CAST(
     ROW('netflix.com', ROW(ARRAY[
       MAP(ARRAY['term', 'count'], ARRAY['cartoons', '1020']),
       MAP(ARRAY['term', 'count'], ARRAY['house of cards', '112042']),
       MAP(ARRAY['term', 'count'], ARRAY['orange is the new black', '342']),
       MAP(ARRAY['term', 'count'], ARRAY['iot', '4'])
     ])
     ) AS ROW(hostname VARCHAR, flaggedActivity ROW(flags ARRAY(MAP(VARCHAR, VARCHAR)) ))
   )
 ] AS items
),
sites AS (
  SELECT sites.hostname, sites.flaggedactivity
  FROM dataset, UNNEST(items) t(sites)
)
SELECT hostname
FROM sites, UNNEST(sites.flaggedActivity.flags) t(flags)
WHERE regexp_like(flags['term'], 'politics|bigdata')
GROUP BY (hostname)
```
This query returns two sites:  

```
+----------------+
| hostname       |
+----------------+
| aws.amazon.com |
+----------------+
| news.cnn.com   |
+----------------+
```

**Example 2: Using `regexp_like`**  
The query in the following example adds up the total popularity scores for the sites matching your search terms with the `regexp_like` function, and then orders them from highest to lowest\.   

```
WITH dataset AS (
  SELECT ARRAY[
    CAST(
      ROW('aws.amazon.com', ROW(ARRAY[
          MAP(ARRAY['term', 'count'], ARRAY['bigdata', '10']),
          MAP(ARRAY['term', 'count'], ARRAY['serverless', '50']),
          MAP(ARRAY['term', 'count'], ARRAY['analytics', '82']),
          MAP(ARRAY['term', 'count'], ARRAY['iot', '74'])
      ])
      ) AS ROW(hostname VARCHAR, flaggedActivity ROW(flags ARRAY(MAP(VARCHAR, VARCHAR)) ))
    ),
    CAST(
      ROW('news.cnn.com', ROW(ARRAY[
        MAP(ARRAY['term', 'count'], ARRAY['politics', '241']),
        MAP(ARRAY['term', 'count'], ARRAY['technology', '211']),
        MAP(ARRAY['term', 'count'], ARRAY['serverless', '25']),
        MAP(ARRAY['term', 'count'], ARRAY['iot', '170'])
      ])
      ) AS ROW(hostname VARCHAR, flaggedActivity ROW(flags ARRAY(MAP(VARCHAR, VARCHAR)) ))
    ),
    CAST(
      ROW('netflix.com', ROW(ARRAY[
        MAP(ARRAY['term', 'count'], ARRAY['cartoons', '1020']),
        MAP(ARRAY['term', 'count'], ARRAY['house of cards', '112042']),
        MAP(ARRAY['term', 'count'], ARRAY['orange is the new black', '342']),
        MAP(ARRAY['term', 'count'], ARRAY['iot', '4'])
      ])
      ) AS ROW(hostname VARCHAR, flaggedActivity ROW(flags ARRAY(MAP(VARCHAR, VARCHAR)) ))
    )
  ] AS items
),
sites AS (
  SELECT sites.hostname, sites.flaggedactivity
  FROM dataset, UNNEST(items) t(sites)
)
SELECT hostname, array_agg(flags['term']) AS terms, SUM(CAST(flags['count'] AS INTEGER)) AS total
FROM sites, UNNEST(sites.flaggedActivity.flags) t(flags)
WHERE regexp_like(flags['term'], 'politics|bigdata')
GROUP BY (hostname)
ORDER BY total DESC
```
This query returns two sites:  

```
+------------------------------------+
| hostname       | terms    | total  |
+----------------+-------------------+
| news.cnn.com   | politics |  241   |
+----------------+-------------------+
| aws.amazon.com | bigdata |  10    |
+----------------+-------------------+
```