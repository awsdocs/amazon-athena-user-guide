# Using a Default Catalog in External Hive Metastore Queries<a name="datastores-hive-default-catalog"></a>

When you run DML and DDL queries on external Hive metastores, you can simplify your query syntax by omitting the catalog name if that name is selected in the query editor\. Certain restrictions apply to this functionality\.

## DML Statements<a name="datastores-hive-default-catalog-dml-statements"></a>

**To run queries with registered catalogs**

1. You can put the catalog name before the database using the syntax `[[catalog_name].database_name].table_name`, as in the following example\.

   ```
   select * from  "hms-catalog-1".hms_tpch.customer limit 10;
   ```  
![\[A DML query with a fully qualified catalog name.\]](http://docs.aws.amazon.com/athena/latest/ug/images/datastores-hive-default-catalog-1.png)

1. When the catalog that you want to use is already selected as your data source, you can omit the catalog name from the query, as in the following example\.

   ```
   select * from hms_tpch.customer limit 10:
   ```  
![\[A DML query using a default catalog.\]](http://docs.aws.amazon.com/athena/latest/ug/images/datastores-hive-default-catalog-2.png)

1. For multiple catalogs, you can omit only the default catalog name\. Specify the full name for any non\-default catalogs\. For example, the `FROM` statement in the following query omits the catalog name for AWS Glue catalog, but it fully qualifies the first two catalog names\.

   ```
   ...
   FROM ehms01.hms_tpch.customer,
            "hms-catalog-1".hms_tpch.orders,
            hms_tpch.lineitem
   ...
   ```  
![\[A DML query with default and non-default catalogs.\]](http://docs.aws.amazon.com/athena/latest/ug/images/datastores-hive-default-catalog-3.jpg)

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

As with DML statements, when you select the catalog and the database in the **Data source** panel, you can omit the catalog prefix from the query\.

In the following example, the data source and database are selected in the query editor\. The *show create table customer* statement succeeds when the `hms-catalog-1` prefix and the `hms_tpch` database name are omitted from the query\.

![\[A DDL statement using the default catalog.\]](http://docs.aws.amazon.com/athena/latest/ug/images/datastores-hive-default-catalog-4.png)