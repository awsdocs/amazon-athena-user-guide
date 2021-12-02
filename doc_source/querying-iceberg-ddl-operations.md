# DDL Operations<a name="querying-iceberg-ddl-operations"></a>


|  | 
| --- |
| The Apache Iceberg feature in Athena is in preview release and is subject to change\. To avoid potential data loss or corruption, do not use this preview on production datasets\. | 

You can use `ALTER TABLE` statements to set and unset Iceberg table properties\. In accordance with [Iceberg specifications](https://iceberg.apache.org/#spec/#table-metadata-fields), table properties are stored in the Iceberg table metadata file rather than in AWS Glue\. Athena does not accept custom table properties\. Refer to the [Table Properties](querying-iceberg-creating-tables.md#querying-iceberg-table-properties) section for allowed key\-value pairs\. 

**Note**  
This preview exposes a small subset of table properties for configuration\. If you would like Athena to support a specific open source table configuration property, send feedback to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com)\.

## ALTER TABLE SET PROPERTIES<a name="querying-iceberg-alter-table-set-properties"></a>

Adds properties to an Iceberg table and sets their assigned values\.

### Synopsis<a name="querying-iceberg-alter-table-set-properties-synopsis"></a>

```
ALTER TABLE table_name SET TBLPROPERTIES ('property_name' = 'property_value' [ , ... ])
```

### Example<a name="querying-iceberg-alter-table-set-properties-example"></a>

```
ALTER TABLE iceberg_table SET TBLPROPERTIES (
  'compaction_bin_pack_target_file_size_bytes'='536870912', 
  'write_compression'='gzip')
```

## ALTER TABLE UNSET PROPERTIES<a name="querying-iceberg-alter-table-unset-properties"></a>

Drops properties from an Iceberg table\. Has no effect if the specified key does not exist\.

### Synopsis<a name="querying-iceberg-alter-table-unset-properties-synopsis"></a>

```
ALTER TABLE table_name UNSET TBLPROPERTIES ('property_name' [ , ... ])
```

### Example<a name="querying-iceberg-alter-table-unset-properties-example"></a>

```
ALTER TABLE iceberg_table UNSET TBLPROPERTIES ('compaction_bin_pack_target_file_size_bytes')
```