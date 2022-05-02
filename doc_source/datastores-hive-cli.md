# Using the AWS CLI with Hive metastores<a name="datastores-hive-cli"></a>

You can use `aws athena` CLI commands to manage the Hive metastore data catalogs that you use with Athena\. After you have defined one or more catalogs to use with Athena, you can reference those catalogs in your `aws athena` DDL and DML commands\.

## Using the AWS CLI to manage Hive metastore catalogs<a name="datastores-hive-cli-manage-hive-catalogs"></a>

### Registering a catalog: Create\-data\-catalog<a name="datastores-hive-cli-registering-a-catalog"></a>

To register a data catalog, you use the `create-data-catalog` command\. Use the `name` parameter to specify the name that you want to use for the catalog\. Pass the ARN of the Lambda function to the `metadata-function` option of the `parameters` argument\. To create tags for the new catalog, use the `tags` parameter with one or more space\-separated `Key=key,Value=value` argument pairs\.

The following example registers the Hive metastore catalog named `hms-catalog-1`\. The command has been formatted for readability\.

```
$ aws athena create-data-catalog 
 --name "hms-catalog-1" 
 --type "HIVE"
 --description "Hive Catalog 1"
 --parameters "metadata-function=arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-v3,sdk-version=1.0" 
 --tags Key=MyKey,Value=MyValue
 --region us-east-1
```

### Showing catalog details: Get\-data\-catalog<a name="datastores-hive-cli-showing-details-of-a-catalog"></a>

To show the details of a catalog, pass the name of the catalog to the `get-data-catalog` command, as in the following example\.

```
$ aws athena get-data-catalog --name "hms-catalog-1" --region us-east-1
```

The following sample result is in JSON format\.

```
{
    "DataCatalog": {
        "Name": "hms-catalog-1",
        "Description": "Hive Catalog 1",
        "Type": "HIVE",
        "Parameters": {
            "metadata-function": "arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-v3",
            "sdk-version": "1.0"
        }
    }
}
```

### Listing registered catalogs: List\-data\-catalogs<a name="datastores-hive-cli-listing-registered-catalogs"></a>

To list the registered catalogs, use the `list-data-catalogs` command and optionally specify a Region, as in the following example\. The catalogs listed always include AWS Glue\.

```
$ aws athena list-data-catalogs --region us-east-1
```

The following sample result is in JSON format\.

```
{
    "DataCatalogs": [
        {
            "CatalogName": "AwsDataCatalog",
            "Type": "GLUE"
        },
        {
            "CatalogName": "hms-catalog-1",
            "Type": "HIVE",
            "Parameters": {
                "metadata-function": "arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-v3",
                "sdk-version": "1.0"
            }
        }
    ]
}
```

### Updating a catalog: Update\-data\-catalog<a name="datastores-hive-cli-updating-a-catalog"></a>

To update a data catalog, use the `update-data-catalog` command, as in the following example\. The command has been formatted for readability\.

```
$ aws athena update-data-catalog 
 --name "hms-catalog-1" 
 --type "HIVE"
 --description "My New Hive Catalog Description" 
 --parameters "metadata-function=arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-new,sdk-version=1.0" 
 --region us-east-1
```

### Deleting a catalog: Delete\-data\-catalog<a name="datastores-hive-cli-deleting-a-catalog"></a>

To delete a data catalog, use the `delete-data-catalog` command, as in the following example\.

```
$ aws athena delete-data-catalog --name "hms-catalog-1" --region us-east-1
```

### Showing database details: Get\-database<a name="datastores-hive-cli-showing-details-of-a-database"></a>

To show the details of a database, pass the name of the catalog and the database to the `get-database` command, as in the following example\.

```
$ aws athena get-database --catalog-name hms-catalog-1 --database-name mydb
```

The following sample result is in JSON format\.

```
{
    "Database": {
        "Name": "mydb",
        "Description": "My database",
        "Parameters": {
            "CreatedBy": "Athena",
            "EXTERNAL": "TRUE"
        }
    }
}
```

### Listing databases in a catalog: List\-databases<a name="datastores-hive-cli-listing-databases"></a>

To list the databases in a catalog, use the `list-databases` command and optionally specify a Region, as in the following example\.

```
$ aws athena list-databases --catalog-name AwsDataCatalog --region us-west-2
```

The following sample result is in JSON format\.

```
{
    "DatabaseList": [
        {
            "Name": "default"
        },
        {
            "Name": "mycrawlerdatabase"
        },
        {
            "Name": "mydatabase"
        },
        {
            "Name": "sampledb",
            "Description": "Sample database",
            "Parameters": {
                "CreatedBy": "Athena",
                "EXTERNAL": "TRUE"
            }
        },
        {
            "Name": "tpch100"
        }
    ]
}
```

### Showing table details: Get\-table\-metadata<a name="datastores-hive-cli-showing-details-of-a-table"></a>

To show the metadata for a table, including column names and datatypes, pass the name of the catalog, database, and table name to the `get-table-metadata` command, as in the following example\.

```
$ aws athena get-table-metadata --catalog-name AwsDataCatalog --database-name mydb --table-name cityuseragent
```

The following sample result is in JSON format\.

```
{
    "TableMetadata": {
        "Name": "cityuseragent",
            "CreateTime": 1586451276.0,
            "LastAccessTime": 0.0,
            "TableType": "EXTERNAL_TABLE",
            "Columns": [
                {
                    "Name": "city",
                    "Type": "string"
                },
                {
                    "Name": "useragent1",
                    "Type": "string"
                }
            ],
            "PartitionKeys": [],
            "Parameters": {
                "COLUMN_STATS_ACCURATE": "false",
                "EXTERNAL": "TRUE",
                "inputformat": "org.apache.hadoop.mapred.TextInputFormat",
                "last_modified_by": "hadoop",
                "last_modified_time": "1586454879",
                "location": "s3://athena-data/",
                "numFiles": "1",
                "numRows": "-1",
                "outputformat": "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat",
                "rawDataSize": "-1",
                "serde.param.serialization.format": "1",
                "serde.serialization.lib": "org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe",
                "totalSize": "61"
            }
        }
}
```

### Showing metadata for all tables in a database: List\-table\-metadata<a name="datastores-hive-cli-showing-all-table-metadata"></a>

To show the metadata for all tables in a database, pass the name of the catalog and database name to the `list-table-metadata` command\. The `list-table-metadata` command is similar to the `get-table-metadata` command, except that you do not specify a table name\. To limit the number of results, you can use the `--max-results` option, as in the following example\. 

```
$ aws athena list-table-metadata --catalog-name AwsDataCatalog --database-name sampledb --region us-east-1 --max-results 2
```

The following sample result is in JSON format\.

```
{
    "TableMetadataList": [
        {
            "Name": "cityuseragent",
            "CreateTime": 1586451276.0,
            "LastAccessTime": 0.0,
            "TableType": "EXTERNAL_TABLE",
            "Columns": [
                {
                    "Name": "city",
                    "Type": "string"
                },
                {
                    "Name": "useragent1",
                    "Type": "string"
                }
            ],
            "PartitionKeys": [],
            "Parameters": {
                "COLUMN_STATS_ACCURATE": "false",
                "EXTERNAL": "TRUE",
                "inputformat": "org.apache.hadoop.mapred.TextInputFormat",
                "last_modified_by": "hadoop",
                "last_modified_time": "1586454879",
                "location": "s3://athena-data/",
                "numFiles": "1",
                "numRows": "-1",
                "outputformat": "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat",
                "rawDataSize": "-1",
                "serde.param.serialization.format": "1",
                "serde.serialization.lib": "org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe",
                "totalSize": "61"
            }
        },
        {
            "Name": "clearinghouse_data",
            "CreateTime": 1589255544.0,
            "LastAccessTime": 0.0,
            "TableType": "EXTERNAL_TABLE",
            "Columns": [
                {
                    "Name": "location",
                    "Type": "string"
                },
                {
                    "Name": "stock_count",
                    "Type": "int"
                },
                {
                    "Name": "quantity_shipped",
                    "Type": "int"
                }
            ],
            "PartitionKeys": [],
            "Parameters": {
                "EXTERNAL": "TRUE",
                "inputformat": "org.apache.hadoop.mapred.TextInputFormat",
                "location": "s3://myjasondata/",
                "outputformat": "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat",
                "serde.param.serialization.format": "1",
                "serde.serialization.lib": "org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe",
                "transient_lastDdlTime": "1589255544"
            }
        }
    ],
    "NextToken": "eyJsYXN0RXZhbHVhdGVkS2V5Ijp7IkhBU0hfS0VZIjp7InMiOiJ0Ljk0YWZjYjk1MjJjNTQ1YmU4Y2I5OWE5NTg0MjFjYTYzIn0sIlJBTkdFX0tFWSI6eyJzIjoiY2xlYXJpbmdob3VzZV9kYXRhIn19LCJleHBpcmF0aW9uIjp7InNlY29uZHMiOjE1ODkzNDIwMjIsIm5hbm9zIjo2NTUwMDAwMDB9fQ=="
}
```

## Running DDL and DML statements<a name="datastores-hive-cli-running-ddl-and-dml"></a>

When you use the AWS CLI to run DDL and DML statements, you can pass the name of the Hive metastore catalog in one of two ways:
+ Directly into the statements that support it\.
+ To the `--query-execution-context` `Catalog` parameter\.

### DDL statements<a name="datastores-hive-cli-ddl-statements"></a>

The following example passes in the catalog name directly as part of the `show create table` DDL statement\. The command has been formatted for readability\.

```
$ aws athena start-query-execution 
 --query-string "show create table hms-catalog-1.hms_tpch_partitioned.lineitem" 
 --result-configuration "OutputLocation=s3://mybucket/lambda/results"
```

The following example DDL `show create table` statement uses the `Catalog` parameter of `--query-execution-context` to pass the Hive metastore catalog name `hms-catalog-1`\. The command has been formatted for readability\.

```
$ aws athena start-query-execution 
 --query-string "show create table lineitem" 
 --query-execution-context "Catalog=hms-catalog-1,Database=hms_tpch_partitioned" 
 --result-configuration "OutputLocation=s3://mybucket/lambda/results"
```

### DML statements<a name="datastores-hive-cli-dml-statements"></a>

The following example DML `select` statement passes the catalog name into the query directly\. The command has been formatted for readability\.

```
$ aws athena start-query-execution
 --query-string "select * from hms-catalog-1.hms_tpch_partitioned.customer limit 100" 
 --result-configuration "OutputLocation=s3://mybucket/lambda/results"
```

The following example DML `select` statement uses the `Catalog` parameter of `--query-execution-context` to pass in the Hive metastore catalog name `hms-catalog-1`\. The command has been formatted for readability\.

```
$ aws athena start-query-execution 
 --query-string "select * from customer limit 100" 
 --query-execution-context "Catalog=hms-catalog-1,Database=hms_tpch_partitioned" 
 --result-configuration "OutputLocation=s3://mybucket/lambda/results"
```