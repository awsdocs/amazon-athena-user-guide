# Using Athena to Query Data Registered With AWS Lake Formation<a name="security-athena-lake-formation"></a>

[AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html) allows you to define and enforce database, table, and column\-level access policies when using Athena queries to read data stored in Amazon S3\. Lake Formation provides an authorization and governance layer on data stored in Amazon S3\. You can use a hierarchy of permissions in Lake Formation to grant or revoke permissions to read data catalog objects such as databases, tables, and columns\. Lake Formation simplifies the management of permissions and allows you to implement fine\-grained access control \(FGAC\) for your data\.

You can use Athena to query both data that is registered with Lake Formation and data that is not registered with Lake Formation\.

Lake Formation permissions apply when using Athena to query source data from Amazon S3 locations that are registered with Lake Formation\. Lake Formation permissions also apply when you create databases and tables that point to registered Amazon S3 data locations\. To use Athena with data registered using Lake Formation, Athena must be configured to use the AWS Glue Data Catalog\.

Lake Formation permissions do not apply when writing objects to Amazon S3, nor do they apply when querying data stored in Amazon S3 or metadata that are not registered with Lake Formation\. For source data in Amazon S3 and metadata that is not registered with Lake Formation, access is determined by IAM permissions policies for Amazon S3 and AWS Glue actions\. Athena query results locations in Amazon S3 cannot be registered with Lake Formation, and IAM permissions policies for Amazon S3 control access\. In addition, Lake Formation permissions do not apply to Athena query history\. You can use Athena workgroups to control access to query history\.

For more information about Lake Formation, see [Lake Formation FAQs](https://aws.amazon.com/lake-formation/faqs/) and the [AWS Lake Formation Developer Guide](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)\.

**Topics**
+ [How Lake Formation Data Access Works](lf-athena-access.md)
+ [Considerations and Limitations](lf-athena-limitations.md)
+ [Managing User Permissions](lf-athena-user-permissions.md)
+ [Applying Lake Formation Permissions to Existing Databases and Tables](lf-athena-removing-permissions.md)
+ [Using Lake Formation and JDBC or ODBC for Federated Access](security-athena-lake-formation-jdbc.md)