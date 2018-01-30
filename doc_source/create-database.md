# CREATE DATABASE<a name="create-database"></a>

Creates a database\. The use of `DATABASE` and `SCHEMA` is interchangeable\. They mean the same thing\.

## Synopsis<a name="synopsis"></a>

```
CREATE (DATABASE|SCHEMA) [IF NOT EXISTS] database_name
  [COMMENT 'database_comment']
  [LOCATION 'S3_loc']
  [WITH DBPROPERTIES ('property_name' = 'property_value') [, ...]]
```

## Parameters<a name="parameters"></a>

**\[IF NOT EXISTS\]**  
Causes the error to be suppressed if a database named `database_name` already exists\.

**\[COMMENT database\_comment\]**  
Establishes the metadata value for the built\-in metadata property named `comment` and the value you provide for `database_comment`\.

**\[LOCATION S3\_loc\]**  
Specifies the location where database files and metastore will exist as `S3_loc`\. The location must be an Amazon S3 location\.

**\[WITH DBPROPERTIES \('property\_name' = 'property\_value'\) \[, \.\.\.\] \]**  
Allows you to specify custom metadata properties for the database definition\.

## Examples<a name="examples"></a>

```
CREATE DATABASE clickstreams;
```

```
CREATE DATABASE IF NOT EXISTS clickstreams
  COMMENT 'Site Foo clickstream data aggregates'
  LOCATION 's3://myS3location/clickstreams'
  WITH DBPROPERTIES ('creator'='Jane D.', 'Dept.'='Marketing analytics');
```