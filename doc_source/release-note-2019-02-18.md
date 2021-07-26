# February 18, 2019<a name="release-note-2019-02-18"></a>

Published on *2019\-02\-18*

Added ability to control query costs by running queries in workgroups\. For information, see [Using Workgroups to Control Query Access and Costs](manage-queries-control-costs-with-workgroups.md)\. Improved the JSON OpenX SerDe used in Athena, fixed an issue where Athena did not ignore objects transitioned to the `GLACIER` storage class, and added examples for querying Network Load Balancer logs\.

Made the following changes:
+ Added support for workgroups\. Use workgroups to separate users, teams, applications, or workloads, and to set limits on amount of data each query or the entire workgroup can process\. Because workgroups act as IAM resources, you can use resource\-level permissions to control access to a specific workgroup\. You can also view query\-related metrics in Amazon CloudWatch, control query costs by configuring limits on the amount of data scanned, create thresholds, and trigger actions, such as Amazon SNS alarms, when these thresholds are breached\. For more information, see [Using Workgroups for Running Queries](workgroups.md) and [Controlling Costs and Monitoring Queries with CloudWatch Metrics and Events](control-limits.md)\.

  Workgroups are an IAM resource\. For a full list of workgroup\-related actions, resources, and conditions in IAM, see [Actions, Resources, and Condition Keys for Amazon Athena](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonathena.html) in the *Service Authorization Reference*\. Before you create new workgroups, make sure that you use [workgroup IAM policies](workgroups-iam-policy.md), and the [**AmazonAthenaFullAccess** Managed Policy](managed-policies.md#amazonathenafullaccess-managed-policy)\. 

  You can start using workgroups in the console, with the [workgroup API operations](workgroups-api-list.md), or with the JDBC driver\. For a high\-level procedure, see [Setting up Workgroups](workgroups-procedure.md)\. To download the JDBC driver with workgroup support, see [Using Athena with the JDBC Driver](connect-with-jdbc.md)\.

  If you use workgroups with the JDBC driver, you must set the workgroup name in the connection string using the `Workgroup` configuration parameter as in the following example:

  ```
  jdbc:awsathena://AwsRegion=<AWSREGION>;UID=<ACCESSKEY>;
  PWD=<SECRETKEY>;S3OutputLocation=s3://<athena-output>-<AWSREGION>/;
  Workgroup=<WORKGROUPNAME>;
  ```

  There are no changes in the way you run SQL statements or make JDBC API calls to the driver\. The driver passes the workgroup name to Athena\.

  For information about differences introduced with workgroups, see [Athena Workgroup APIs](workgroups-api-list.md) and [Troubleshooting Workgroups](workgroups-troubleshooting.md)\.
+ Improved the JSON OpenX SerDe used in Athena\. The improvements include, but are not limited to, the following:
  + Support for the `ConvertDotsInJsonKeysToUnderscores` property\. When set to `TRUE`, it allows the SerDe to replace the dots in key names with underscores\. For example, if the JSON dataset contains a key with the name `"a.b"`, you can use this property to define the column name to be `"a_b"` in Athena\. The default is `FALSE`\. By default, Athena does not allow dots in column names\.
  + Support for the `case.insensitive` property\. By default, Athena requires that all keys in your JSON dataset use lowercase\. Using `WITH SERDE PROPERTIES ("case.insensitive"= FALSE;)` allows you to use case\-sensitive key names in your data\. The default is `TRUE`\. When set to `TRUE`, the SerDe converts all uppercase columns to lowercase\. 

  For more information, see [OpenX JSON SerDe](json-serde.md#openx-json-serde)\.
+ Fixed an issue where Athena returned `"access denied"` error messages, when it processed Amazon S3 objects that were archived to Glacier by Amazon S3 lifecycle policies\. As a result of fixing this issue, Athena ignores objects transitioned to the `GLACIER` storage class\. Athena does not support querying data from the `GLACIER` storage class\. 

   For more information, see [Requirements for Tables in Athena and Data in Amazon S3](creating-tables.md#s3-considerations) and [Transitioning to the GLACIER Storage Class \(Object Archival\) ](https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-transition-general-considerations.html#before-deciding-to-archive-objects) in the *Amazon Simple Storage Service Developer Guide*\.
+ Added examples for querying Network Load Balancer access logs that receive information about the Transport Layer Security \(TLS\) requests\. For more information, see [Querying Network Load Balancer Logs](networkloadbalancer-classic-logs.md)\.