# Creating Tables Based on Encrypted Datasets in Amazon S3<a name="creating-tables-based-on-encrypted-datasets-in-s3"></a>

When you create a table, indicate to Athena that a dataset is encrypted in Amazon S3\. This is not required when using SSE\-KMS\. For both SSE\-S3 and AWS KMS encryption, Athena determines the proper materials to use to decrypt the dataset and create the table, so you don't need to provide key information\.

Users that run queries, including the user who creates the table, must have the appropriate permissions as described earlier in this topic\.

**Important**  
If you use Amazon EMR along with EMRFS to upload encrypted Parquet files, you must disable multipart uploads by setting `fs.s3n.multipart.uploads.enabled` to `false`\. If you don't do this, Athena is unable to determine the Parquet file length and a **HIVE\_CANNOT\_OPEN\_SPLIT** error occurs\. For more information, see [Configure Multipart Upload for Amazon S3](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-upload-s3.html#Config_Multipart) in the *Amazon EMR Management Guide*\.

Indicate that the dataset is encrypted in Amazon S3 in one of the following ways\. This step is not required if SSE\-KMS is used\.
+ Use the [CREATE TABLE](create-table.md) statement with a `TBLPROPERTIES` clause that specifies `'has_encrypted_data'='true'`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypt_has_encrypted.png)
+ Use the [JDBC driver](connect-with-jdbc.md) and set the `TBLPROPERTIES` value as shown in the previous example, when you execute [CREATE TABLE](create-table.md) using `statement.executeQuery()`\. 
+ Use the **Add table** wizard in the Athena console, and then choose **Encrypted data set** when you specify a value for **Location of input data set**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypt_has_encrypted_console.png)

Tables based on encrypted data in Amazon S3 appear in the **Database** list with an encryption icon\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/encrypted_table_icon.png)