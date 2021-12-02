# November 18, 2021<a name="release-note-2021-11-18"></a>

Published on 2021\-11\-18

Athena announces new features and improvements\.
+ Support for spill\-to\-disk for aggregation queries that contain `DISTINCT`, `ORDER BY`, or both, as in the following example:

  ```
  SELECT array_agg(orderstatus ORDER BY orderstatus)
  FROM orders
  GROUP BY orderpriority, custkey
  ```
+ Addressed memory handling issues for queries that use `DISTINCT`\. To avoid error messages like Query exhausted resources at this scale factor when you use `DISTINCT` queries, choose columns that have a low cardinality for `DISTINCT`, or reduce the data size of the query\.
+ In `SELECT COUNT(*)` queries that do not specify a specific column, improved performance and memory usage by keeping only the count without row buffering\.
+ Introduced the following string functions\.
  + `translate(source, from, to)` – Returns the `source` string with the characters found in the `from` string replaced by the corresponding characters in the `to` string\. If the `from` string contains duplicates, only the first is used\. If the `source` character does not exist in the `from` string, the `source` character is copied without translation\. If the index of the matching character in the `from` string is greater than the length of the `to` string, the character is omitted from the resulting string\.
  + `concat_ws(string0, array(varchar))` – Returns the concatenation of elements in the array using `string0` as a separator\. If `string0` is null, then the return value is null\. Any null values in the array are skipped\.
+ Fixed a bug in which queries failed when trying to access a missing subfield in a `struct`\. Queries now return a null for the missing subfield\.
+ Fixed an issue with inconsistent hashing for the decimal data type\.
+ Fixed an issue that caused exhausted resources when there were too many columns in a partition\.