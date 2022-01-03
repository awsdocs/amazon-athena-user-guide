# Using a Default Data Source Name in External Hive Metastore Queries<a name="datastores-hive-default-catalog"></a>

When you run DML and DDL queries on external Hive metastores, you can simplify your query syntax by omitting the catalog name if that name is selected in the query editor\. Certain restrictions apply to this functionality\.

## DML Statements<a name="datastores-hive-default-catalog-dml-statements"></a>

**To run queries with registered catalogs**

1. You can put the data source name before the database using the syntax `[[data_source_name].database_name].table_name`, as in the following example\.

   ```
   select * from  "hms-catalog-1".hms_tpch.customer limit 10;
   ```

1. When the data source that you want to use is already selected in the query editor, you can omit the name from the query, as in the following example\.

   ```
   select * from hms_tpch.customer limit 10:
   ```  
![\[A DML query using a default data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/datastores-hive-default-catalog-2.png)

1. When you use multiple data sources in a query, you can omit only the default data source name, and must specify the full name for any non\-default data sources\. 

   For example, suppose `AwsDataCatalog` is selected as the default data source in the query editor\. The `FROM` statement in the following query excerpt fully qualifies the first two data source names but omits the name for the third data source because it is in the AWS Glue data catalog\.

   ```
   ...
   FROM ehms01.hms_tpch.customer,
            "hms-catalog-1".hms_tpch.orders,
            hms_tpch.lineitem
   ...
   ```

## DDL Statements<a name="datastores-hive-default-catalog-ddl-statements"></a>

The following Athena DDL statements support catalog name prefixes\. Catalog name prefixes in other DDL statements cause syntax errors\.

```
SHOW TABLES [IN [catalog_name.]database_name] ['regular_expression']

SHOW TBLPROPERTIES [[catalog_name.]database_name.]table_name [('property_name')]

SHOW COLUMNS IN [[catalog_name.]database_name.]table_name

SHOW PARTITIONS [[catalog_name.]database_name.]table_name

SHOW CREATE TABLE [[catalog_name.][database_name.]table_name

DESCRIBE [EXTENDED | FORMATTED] [[catalog_name.][database_name.]table_name [PARTITION partition_spec] [col_name ( [.field_name] | [.'$elem$'] | [.'$key$'] | [.'$value$'] )]
```

As with DML statements, you can omit the datasource and database prefixes from the query when the data source and database are selected in the query editor\.

In the following image, the `hms-catalog-1` data source and the `hms_tpch` database are selected in the query editor\. The `show create table customer` statement succeeds even though the `hms-catalog-1` prefix and the `hms_tpch` database name are omitted from the query itself\.

![\[A DDL statement using the default catalog.\]](http://docs.aws.amazon.com/athena/latest/ug/images/datastores-hive-default-catalog-4.png)

## Specifying a Default Data Source in a JDBC Connection String<a name="datastores-hive-default-catalog-jdbc"></a>

When you use the Athena JDBC Driver to connect Athena to an external Hive metastore, you can use the `Catalog` parameter to specify the default data source name in your connection string in a SQL editor like [SQL Workbench](https://www.sql-workbench.eu/index.html)\.

**Note**  
To download the latest Athena JDBC drivers, see [Using Athena with the JDBC Driver](https://docs.aws.amazon.com/athena/latest/ug/connect-with-jdbc.html)\.

The following connection string specifies the default data source *hms\-catalog\-name*\.

```
    jdbc:awsathena://AwsRegion=us-east-1;S3OutputLocation=s3://<location>/lambda/results/;Workgroup=AmazonAthenaPreviewFunctionality;Catalog=hms-catalog-name;
```

The following image shows a sample JDBC connection URL as configured in SQL Workbench\.

![\[Configuring a JDBC connection URL in SQL Workbench.\]](http://docs.aws.amazon.com/athena/latest/ug/images/datastores-hive-default-catalog-jdbc-1.jpg)