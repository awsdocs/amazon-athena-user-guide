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
+ If you have created Athena views in the Data Catalog, then Data Catalog treats views as tables\. You can use table level fine\-grained access control in Data Catalog to [restrict access](fine-grained-access-to-glue-resources.md) to these views\. 
+  Athena prevents you from running recursive views and displays an error message in such cases\. A recursive view is a view query that references itself\.
+ Athena detects stale views and displays an error message in such cases\. A stale view is a view query that references tables or databases that do not exist\.
+ You can create and run nested views as long as the query behind the nested view is valid and the tables and databases exist\.