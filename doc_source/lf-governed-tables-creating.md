# Creating Governed Tables<a name="lf-governed-tables-creating"></a>

## CREATE TABLE Syntax<a name="lf-governed-tables-create-table-syntax"></a>

To create a governed table from Athena, set the `table_type` table property to `LAKEFORMATION_GOVERNED` in the `TBL_PROPERTIES` clause, as in the following syntax summary\.

```
CREATE TABLE 
  [db_name.]table_name (col_name data_type [COMMENT col_comment] [, ...] ) 
  LOCATION 's3://DOC-EXAMPLE-BUCKET/[your-folder/]' 
  TBLPROPERTIES ('table_type'='LAKEFORMATION_GOVERNED' 
    [, property_name=property_value] )
```

**Note**  
The `EXTERNAL` keyword is not used in Athena to create governed tables\. Using it results in the error message External keyword not supported for table type LAKEFORMATION\_GOVERNED\.

## Example CREATE TABLE Statement<a name="lf-governed-tables-create-table-example"></a>

The following example creates a governed table that has three columns\.

```
CREATE TABLE governed_table (
  id int,
  data string,
  category string) 
PARTITIONED BY (category) 
LOCATION 's3://DOC-EXAMPLE-BUCKET/governed-folder' 
TBLPROPERTIES (
  'table_type'='LAKEFORMATION_GOVERNED'
)
```