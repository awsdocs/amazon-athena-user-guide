# Considerations for Views<a name="considerations-views"></a>

The following considerations apply to creating and using views in Athena:
+ In Athena, you can preview and work with views created in the Athena Console, in the AWS Glue Data Catalog, if you have migrated to using it, or with Presto running on the Amazon EMR cluster connected to the same catalog\. You cannot preview or add to Athena views that were created in other ways\.
+  If you are creating views through the AWS GlueData Catalog, you must include the `PartitionKeys` parameter and set its value to an empty list, as follows: `PartitionKeys":[]`\. Otherwise, your view query will fail in Athena\. The following example shows a view created from the Data Catalog with `PartitionKeys":[]`:

  ```
  aws glue create-table 
  --database-name mydb 
  --table-input '{
  "Name":"test",
    "TableType": "EXTERNAL_TABLE",  
    "Owner": "hadoop",  
    "StorageDescriptor":{
       "Columns":[{
             "Name":"a","Type":"string"},{"Name":"b","Type":"string"}],
     "Location":"s3://xxxxx/Oct2018/25Oct2018/",
     "InputFormat":"org.apache.hadoop.mapred.TextInputFormat", 
     "OutputFormat": "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat", 
     "SerdeInfo":{"SerializationLibrary":"org.apache.hadoop.hive.serde2.OpenCSVSerde",
     "Parameters":{"separatorChar": "|", "serialization.format": "1"}}},"PartitionKeys":[]}'
  ```
+  Athena prevents you from running recursive views and displays an error message in such cases\. A recursive view is a view query that references itself\.
+ Athena detects stale views and displays an error message in such cases\. A stale view is a view query that references tables or databases that do not exist\.
+ You can create and run nested views as long as the query behind the nested view is valid and the tables and databases exist\.
+ Athena view names cannot contain special characters, other than underscore `(_)`\. For more information, see [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)\.
+ Avoid using reserved keywords for naming views\. If you use reserved keywords, use double quotes to enclose reserved keywords in your queries on views\. See [Reserved Keywords](reserved-words.md)\.
+ You cannot use views with geospatial functions\.
+ You cannot use views to manage access control on data in Amazon S3\. To query a view, you need permissions to access the data stored in Amazon S3\. For more information, see [Access to Amazon S3](s3-permissions.md)\.