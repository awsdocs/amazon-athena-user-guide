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
Establishes the metadata value for the built\-in metadata property named `comment` and the value you provide for `database_comment`\. In AWS Glue, the `COMMENT` contents are written to the `Description` field of the database properties\.

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
  LOCATION 's3://myS3location/clickstreams/'
  WITH DBPROPERTIES ('creator'='Jane D.', 'Dept.'='Marketing analytics');
```

## Viewing Database Properties<a name="create-database-viewing-properties"></a>

To view the database properties for a database that you create in AWSDataCatalog using `CREATE DATABASE`, you can use the AWS CLI command [aws glue get\-database](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/glue/get-database.html), as in the following example:

```
aws glue get-database --name <your-database-name>
```

In JSON output, the result looks like the following:

```
{
    "Database": {
        "Name": "<your-database-name>",
        "Description": "<your-database-comment>",
        "LocationUri": "s3://<your-database-location>",
        "Parameters": {
            "<your-database-property-name>": "<your-database-property-value>"
        },
        "CreateTime": 1603383451.0,
        "CreateTableDefaultPermissions": [
            {
                "Principal": {
                    "DataLakePrincipalIdentifier": "IAM_ALLOWED_PRINCIPALS"
                },
                "Permissions": [
                    "ALL"
                ]
            }
        ]
    }
}
```

For more information about the AWS CLI, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.