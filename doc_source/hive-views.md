# Working with Hive Views<a name="hive-views"></a>

You can use Athena to query existing views in your external Apache Hive metastores\. Athena translates your views for you on\-the\-fly at runtime without changing the original view or storing the translation\.

For example, suppose you have a Hive view like the following that uses a syntax not supported in Athena like `LATERAL VIEW explode()`:

```
CREATE VIEW team_view AS 
SELECT team, score 
FROM matches 
LATERAL VIEW explode(scores) m AS score
```

Athena translates the Hive view query string into a statement like the following that Athena can run:

```
SELECT team, score
FROM matches
CROSS JOIN UNNEST(scores) AS m (score)
```

For information on connecting an external Hive metastore to Athena, see [Using Athena Data Connector for External Hive Metastore](connect-to-data-source-hive.md)\.

## Considerations and Limitations<a name="hive-views-considerations-and-limitations"></a>

When querying Hive views from Athena, consider the following points:
+ Athena does not support creating Hive views\. You can create Hive views in your external Hive metastore, which you can then query from Athena\.
+ Athena does not support custom UDFs for Hive views\.
+ Due to a known issue in the Athena console, Hive views appear under the list of tables instead of the list of views\.
+ Although the translation process is automatic, certain Hive functions are not supported for Hive views or require special handling\. For more information, see the following section\.

## Hive Function Support Limitations<a name="hive-views-function-limitations"></a>

This section highlights the Hive functions that Athena does not support for Hive views or that require special treatment\. Currently, because Athena primarily supports functions from Hive 2\.2\.0, functions that are available only in higher versions \(such as Hive 4\.0\.0\) are not available\. For a full list of Hive functions, see [Hive Language Manual UDF](https://cwiki.apache.org/confluence/display/hive/languagemanual+udf)\.

### Aggregate Functions<a name="hive-views-aggregate-functions"></a>

#### Aggregate Functions That Require Special Handling<a name="hive-views-aggregate-functions-special-handling"></a>

The following aggregate function for Hive views requires special handling\.
+ **avg** – Instead of `avg(INT i)`, use `avg(CAST(i AS DOUBLE))`\.

#### Aggregate Functions Not Supported<a name="hive-views-aggregate-functions-not-supported"></a>

The following Hive aggregate functions are not supported in Athena for Hive views\.

```
covar_pop
histogram_numeric
ntile
percentile
percentile_approx
```

Regression functions like `regr_count`, `regr_r2`, and `regr_sxx` are not supported in Athena for Hive views\.

### Date Functions Not Supported<a name="hive-views-date-functions-not-supported"></a>

The following Hive date functions are not supported in Athena for Hive views\.

```
date_format(date/timestamp/string ts, string fmt)
day(string date)
dayofmonth(date)
extract(field FROM source)
hour(string date)
minute(string date)
month(string date)
quarter(date/timestamp/string)
second(string date)
weekofyear(string date)
year(string date)
```

### Masking Functions Not Supported<a name="hive-views-masking-functions-not-supported"></a>

Hive masking functions like `mask()`, and `mask_first_n()` are not supported in Athena for Hive views\.

### Miscellaneous Functions<a name="hive-views-miscellaneous-functions"></a>

#### Miscellaneous Functions That Require Special Handling<a name="hive-views-supported-miscellaneous-functions-special-handling"></a>

The following miscellaneous functions for Hive views require special handling\.
+ **md5** – Athena supports `md5(binary)` but not `md5(varchar)`\.
+ **explode** – Athena supports `explode` when it is used in the following syntax:

  ```
  LATERAL VIEW [OUTER] EXPLODE(<argument>)
  ```
+ **posexplode** – Athena supports `posexplode` when it is used in the following syntax:

  ```
  LATERAL VIEW [OUTER] POSEXPLODE(<argument>)           
  ```

  In the `(pos, val)` output, Athena treats the `pos` column as `BIGINT`\. Because of this, you may need to cast the `pos` column to `BIGINT` to avoid a stale view\. The following example illustrates this technique\.

  ```
  SELECT CAST(c AS BIGINT) AS c_bigint, d 
  FROM table LATERAL VIEW POSEXPLODE(<argument>) t AS c, d
  ```

#### Miscellaneous Functions Not Supported<a name="hive-views-unsupported-miscellaneous-functions-not-supported"></a>

The following Hive functions are not supported in Athena for Hive views\.

```
aes_decrypt
aes_encrypt
current_database
current_user
inline
java_method
logged_in_user
reflect
sha/sha1/sha2
stack
version
```

### Operators<a name="hive-views-operators"></a>

#### Operators That Require Special Handling<a name="hive-views-operators-special-handling"></a>

The following operators for Hive views require special handling\.
+ **mod operator \(%\)** – Because the `DOUBLE` type implicitly casts to `DECIMAL(x,y)`, the following syntax can cause a View is stale error message:

  ```
  a_double % 1.0 AS column
  ```

  To work around this issue, use `CAST`, as in the following example\.

  ```
  CAST(a_double % 1.0 as DOUBLE) AS column
  ```
+ **division operator \(/\)** – In Hive, `int` divided by `int` produces a `double`\. In Athena, the same operation produces a truncated `int`\.

#### Operators Not Supported<a name="hive-views-operators-not-supported"></a>

Athena does not support the following operators for Hive views\.

**\~A** – bitwise `NOT`

**A ^ B** – bitwise `XOR`

**A & B** – bitwise `AND`

**A \| B** – bitwise `OR`

**A <=> B** – Returns same result as the equals \(`=`\) operator for non\-null operands\. Returns `TRUE` if both are `NULL`, `FALSE` if one of them is `NULL`\.

### String Functions<a name="hive-views-string-functions"></a>

#### String Functions That Require Special Handling<a name="hive-views-string-functions-special-handling"></a>

The following Hive string functions for Hive views require special handling\.
+ **chr\(bigint\|double A\)** – Hive allows negative arguments; Athena does not\.
+ **instr\(string str, string substr\)** – Because the Athena mapping for the `instr` function returns `BIGINT` instead of `INT`, use the following syntax:

  ```
  CAST(instr(string str, string substr) as INT)         
  ```

  Without this step, the view will be considered stale\.
+ **length\(string A\)** – Because the Athena mapping for the `length` function returns `BIGINT` instead of `INT`, use the following syntax so that the view will not be considered stale:

  ```
  CAST(length(string str) as INT)
  ```

#### String Functions Not Supported<a name="hive-views-string-functions-not-supported"></a>

The following Hive string functions are not supported in Athena for Hive views\.

```
ascii(string str)
character_length(string str)
decode(binary bin, string charset)
encode(string src, string charset)
elt(N int,str1 string,str2 string,str3 string,...)
field(val T,val1 T,val2 T,val3 T,...)
find_in_set(string str, string strList)
initcap(string A)
levenshtein(string A, string B)
locate(string substr, string str[, int pos])
octet_length(string str)
parse_url(string urlString, string partToExtract [, string keyToExtract])
printf(String format, Obj... args)
quote(String text)
regexp_extract(string subject, string pattern, int index)
repeat(string str, int n)
sentences(string str, string lang, string locale)
soundex(string A)
space(int n)
str_to_map(text[, delimiter1, delimiter2])
substring_index(string A, string delim, int count)
```

### XPath Functions Not Supported<a name="hive-views-xpath-functions-not-supported"></a>

Hive XPath functions like `xpath`, `xpath_short`, and `xpath_int` are not supported in Athena for Hive views\.

## Troubleshooting<a name="hive-views-troubleshooting"></a>

When you use Hive views in Athena, you may encounter the following issues:
+ **View *<view name>* is stale** – This message usually indicates a type mismatch between the view in Hive and Athena\. If the same function in the [Hive LanguageManual UDF](https://cwiki.apache.org/confluence/display/hive/languagemanual+udf) and [Presto Functions and Operators](https://prestodb.io/docs/current/functions.html) documentation has different signatures, try casting the mismatched data type\.
+ **Function not registered** – Athena does not currently support the function\. For details, see the information earlier in this document\.