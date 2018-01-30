# Grok SerDe<a name="grok"></a>

The Logstash Grok SerDe is a library with a set of specialized patterns for deserialization of unstructured text files, usually logs\. Each Grok pattern is a named regular expression\. You can identify and re\-use these deserialization patterns as needed\. This makes it easier to use Grok compared with using regular expressions\. Grok provides a set of [pre\-defined patterns](https://github.com/elastic/logstash/blob/v1.4.2/patterns/grok-patterns)\. You can also create custom patterns\.

To specify the Grok SerDe when creating a table in Athena, use the `ROW FORMAT SERDE 'com.amazonaws.glue.serde.GrokSerDe'` clause, followed by the `WITH SERDEPROPERTIES` clause that specifies the patterns to match in your data, where:

+ The `input.format` expression defines the patterns to match in the data file\. It is required\.

+ The `input.grokCustomPatterns` expression defines a named custom pattern, which you can subsequently use within the `input.format` expression\. It is optional\.

+ The `STORED AS INPUTFORMAT` and `OUTPUTFORMAT` clauses are required\.

+ The `LOCATION` clause specifies an Amazon S3 bucket, which can contain multiple source data files\. All files in the bucket are deserialized to create the table\.

## Examples<a name="examples"></a>

These examples rely on the list of predefined Grok patterns\. See [pre\-defined patterns](https://github.com/elastic/logstash/blob/v1.4.2/patterns/grok-patterns)\.

### Example 1<a name="example-1"></a>

This example uses a single fictional text file saved in `s3://mybucket/groksample` with the following data, which represents Postfix maillog entries\.

```
Feb  9 07:15:00 m4eastmail postfix/smtpd[19305]: B88C4120838: connect from unknown[192.168.55.4]
Feb  9 07:15:00 m4eastmail postfix/smtpd[20444]: B58C4330038: client=unknown[192.168.55.4]
Feb  9 07:15:03 m4eastmail postfix/cleanup[22835]: BDC22A77854: message-id=<31221401257553.5004389LCBF@m4eastmail.example.com>
```

The following statement creates a table in Athena called `mygroktable` from the source data file, using a custom pattern and the predefined patterns that you specify\.

```
CREATE EXTERNAL TABLE `mygroktable`(
   'SYSLOGBASE' string,
   'queue_id' string,
   'syslog_message' string
   )
ROW FORMAT SERDE
   'com.amazonaws.glue.serde.GrokSerDe'
WITH SERDEPROPERTIES (
   'input.grokCustomPatterns' = 'POSTFIX_QUEUEID [0-9A-F]{7,12}',
   'input.format'='%{SYSLOGBASE} %{POSTFIX_QUEUEID:queue_id}: %{GREEDYDATA:syslog_message}'
   )
STORED AS INPUTFORMAT
   'org.apache.hadoop.mapred.TextInputFormat'
OUTPUTFORMAT
   'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
   's3://mybucket/groksample';
```

Start with something simple like `%{NOTSPACE:column}` to get the columns mapped first and then specialize the columns if you want to\.

### Example 2<a name="example-2"></a>

In the following example, you create a query for Log4j logs\. The example log file has the entries in this format:

```
2017-09-12 12:10:34,972 INFO  - processType=AZ, processId=ABCDEFG614B6F5E49, status=RUN,
threadId=123:amqListenerContainerPool23[P:AJ|ABCDE9614B6F5E49||2017-09-12T12:10:11.172-0700],
executionTime=7290, tenantId=12456, userId=123123f8535f8d76015374e7a1d87c3c, shard=testapp1,
jobId=12312345e5e7df0015e777fb2e03f3c, messageType=REAL_TIME_SYNC,
action=receive, hostname=1.abc.def.com
```

To query this log file:

+ Add the Grok pattern to the `input.format` for each column\. For example, for `timestamp`, add `%{TIMESTAMP_ISO8601:timestamp}`\. For `loglevel`, add `%{LOGLEVEL:loglevel}`\.

+ Make sure the pattern in `input.format` matches the format of the log exactly, by mapping the dashes \(`-`\) and the commas that separate the entries in the log format\.

  ```
  CREATE EXTERNAL TABLE bltest (
   timestamp STRING,
   loglevel STRING,
   processtype STRING,
   processid STRING,
   status STRING,
   threadid STRING,
   executiontime INT,
   tenantid INT,
   userid STRING,
   shard STRING,
   jobid STRING,
   messagetype STRING,
   action STRING,
   hostname STRING
   )
  ROW FORMAT SERDE 'com.amazonaws.glue.serde.GrokSerDe'
  WITH SERDEPROPERTIES (
  "input.grokCustomPatterns" = 'C_ACTION receive|send',
  "input.format" = "%{TIMESTAMP_ISO8601:timestamp}, %{LOGLEVEL:loglevel} - processType=%{NOTSPACE:processtype}, processId=%{NOTSPACE:processid}, status=%{NOTSPACE:status}, threadId=%{NOTSPACE:threadid}, executionTime=%{POSINT:executiontime}, tenantId=%{POSINT:tenantid}, userId=%{NOTSPACE:userid}, shard=%{NOTSPACE:shard}, jobId=%{NOTSPACE:jobid}, messageType=%{NOTSPACE:messagetype}, action=%{C_ACTION:action}, hostname=%{HOST:hostname}"
  ) STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'
  OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
  LOCATION 's3://mybucket /samples/';
  ```