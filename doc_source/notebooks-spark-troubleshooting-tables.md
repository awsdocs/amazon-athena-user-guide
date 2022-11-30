# Troubleshooting tables<a name="notebooks-spark-troubleshooting-tables"></a>

## Illegal argument exception when creating a table<a name="notebooks-spark-troubleshooting-tables-illegal-argument-exception"></a>

**Error message**: IllegalArgumentException: Cannot create a path from an empty string\.

**Cause**: This error can occur when you use Apache Spark in Athena to create a table in an AWS Glue database, and the database has an empty `LOCATION` property\. Although the property is marked as optional in the AWS Glue console, it is required for use with Athena for Spark\.

**Suggested Solution**: To fix the error, add an Amazon S3 path to the `LOCATION` property of the existing AWS Glue database or create a database in the AWS Glue Data Catalog that specifies an Amazon S3 path in the `LOCATION` field\.

You can also specify the `LOCATION` when you use Spark SQL to create a table, as in the following example\.

```
spark.sql("create table mytable (firstName STRING) location 's3://DOC-EXAMPLE-BUCKET/'")
```

**Be sure to add the trailing forward slash to the path**  
When you use the SQL `CREATE TABLE` statement to create a table, the path that the location property specifies must be an Amazon S3 path that has a bucket name or a bucket name and one or more folders\. The Amazon S3 path should include a trailing forward slash after the bucket or folder names\. Otherwise, the `CREATE TABLE` statement can fail with the error IllegalArgumentException: Cannot create a path from an empty string\. 

For example, the following statement fails because the path specified does not include a trailing slash after the bucket name \(for example, 's3://*DOC\-EXAMPLE\-BUCKET*/'\): 

```
spark.sql("create table mytable (firstName STRING) location 's3://DOC-EXAMPLE-BUCKET'") 
```

For information about `CREATE TABLE` syntax, see [CREATE TABLE](create-table.md)\.

## AccessDeniedException when querying AWS Glue tables<a name="notebooks-spark-troubleshooting-tables-glue-access-denied"></a>

**Error message**: pyspark\.sql\.utils\.AnalysisException: Unable to verify existence of default database: com\.amazonaws\.services\.glue\.model\.AccessDeniedException: User: arn:aws:sts::*aws\-account\-id*:assumed\-role/AWSAthenaSparkExecutionRole\-*unique\-identifier*/AthenaExecutor\-*unique\-identifier* is not authorized to perform: glue:GetDatabase on resource: arn:aws:glue:*aws\-region*:*aws\-account\-id*:catalog because no identity\-based policy allows the glue:GetDatabase action \(Service: AWSGlue; Status Code: 400; Error Code: AccessDeniedException; Request ID: *request\-id*; Proxy: null\)

**Cause**: The execution role for your Spark\-enabled workgroup is missing permissions to access AWS Glue resources\.

**Suggested Solution**: To resolve this issue, grant your execution role access to AWS Glue resources, and then edit your Amazon S3 bucket policy to grant access to your execution role\.

The following procedure describes these steps in greater detail\.

**To grant your execution role access to AWS Glue resources**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the Athena console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, choose the link of the workgroup that you want to view\.

1. On the **Overview Details** page for the workgroup, choose the **Role ARN** link\. The link opens the Spark execution role in the IAM console\.

1. In the **Permissions policies** section, choose the linked role policy name\.

1. Choose **Edit policy**, and then choose **JSON**\.

1. Add AWS Glue access to the role\. Typically, you add permissions for the `glue:GetDatabase` and `glue:GetTable` actions\. For more information on configuring IAM roles, see [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the IAM User Guide\. 

1. Choose **Review policy**, and then choose **Save changes**\.

1. Edit your Amazon S3 bucket policy to grant access to the execution role\. Note that you must grant the role access to both the bucket and the objects in the bucket\. For steps, see [Adding a bucket policy using the Amazon S3 console](https://docs.aws.amazon.com/AmazonS3/latest/userguide/add-bucket-policy.html) in the Amazon Simple Storage Service User Guide\.