# Regex SerDe<a name="regex-serde"></a>

The Regex SerDe uses a regular expression \(regex\) to deserialize data by extracting regex groups into table columns\. 

If a row in the data does not match the regex, then all columns in the row are returned as `NULL`\. If a row matches the regex but has fewer groups than expected, the missing groups are `NULL`\. If a row in the data matches the regex but has more columns than groups in the regex, the additional columns are ignored\. 

For more information, see [Class RegexSerDe](https://hive.apache.org/javadocs/r1.2.2/api/org/apache/hadoop/hive/serde2/RegexSerDe.html) in the Apache Hive documentation\.

## SerDe Name<a name="serde-name"></a>

RegexSerDe

## Library Name<a name="library-name"></a>

 RegexSerDe 

## Examples<a name="examples"></a>

The following example creates a table from CloudFront logs using the RegExSerDe\. Replace *myregion* in `s3://athena-examples-myregion/cloudfront/plaintext/` with the region identifier where you run Athena \(for example, `s3://athena-examples-us-west-1/cloudfront/plaintext/`\)\.

```
CREATE EXTERNAL TABLE IF NOT EXISTS cloudfront_logs (
  `Date` DATE,
  Time STRING,
  Location STRING,
  Bytes INT,
  RequestIP STRING,
  Method STRING,
  Host STRING,
  Uri STRING,
  Status INT,
  Referrer STRING,
  os STRING,
  Browser STRING,
  BrowserVersion STRING
 ) ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
 WITH SERDEPROPERTIES (
 "input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]([^\;]+).*\%20([^\/]+)[\/](.*)$"
 ) LOCATION 's3://athena-examples-myregion/cloudfront/plaintext/';
```