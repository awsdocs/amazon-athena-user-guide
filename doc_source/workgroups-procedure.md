# Setting up workgroups<a name="workgroups-procedure"></a>

Setting up workgroups involves creating them and establishing permissions for their usage\. First, decide which workgroups your organization needs, and create them\. Next, set up IAM workgroup policies that control user access and actions on a `workgroup` resource\. Users with access to these workgroups can now run queries in them\.

**Note**  
Use these tasks for setting up workgroups when you begin to use them for the first time\. If your Athena account already uses workgroups, each account's user requires permissions to run queries in one or more workgroups in the account\. Before you run queries, check your IAM policy to see which workgroups you can access, adjust your policy if needed, and [switch](workgroups-create-update-delete.md#switching-workgroups) to a workgroup you intend to use\.

By default, if you have not created any workgroups, all queries in your account run in the primary workgroup\.

Athena displays the current workgroup in the **Workgroup** option on the upper right of the console\. You can use this option to switch workgroups\. When you run queries, they run in the current workgroup\. You can run queries in the context of a workgroup in the console, through API operations, through the command line interface, or through a client application by using the JDBC or ODBC driver\. When you have access to a workgroup, you can view the workgroup's settings, metrics, and data usage control limits\. With additional permissions, you can edit the settings and data usage control limits\.

**To set up workgroups**

1.  Decide which workgroups to create\. For example, you can decide the following:
   + Who can run queries in each workgroup, and who owns workgroup configuration\. This determines IAM policies you create\. For more information, see [ IAM policies for accessing workgroups](workgroups-iam-policy.md)\.
   + Which locations in Amazon S3 to use for the query results for queries that run in each workgroup\. A location must exist in Amazon S3 before you can specify it for the workgroup query results\. All users who use a workgroup must have access to this location\. For more information, see [Workgroup settings](workgroups-settings.md)\.
   + Whether the owner of the Amazon S3 query results bucket has full control over new objects that are written to the bucket\. For example, if your query result location is owned by another account, you can grant ownership and full control over your query results to the other account\. For more information, see [AclConfiguration](https://docs.aws.amazon.com/athena/latest/APIReference/API_AclConfiguration.html)\.
   + Specify the ID of the AWS account that you expect to be the owner of the output location bucket\. This is an optional added security measure\. If the account ID of the bucket owner does not match the ID that you specify here, attempts to output to the bucket will fail\. For more information, see [Verifying bucket ownership with bucket owner condition](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-owner-condition.html) in the *Amazon S3 User Guide*\. This setting does not apply to CTAS, INSERT INTO, or UNLOAD statements\.
   + Which encryption settings are required, and which workgroups have queries that must be encrypted\. We recommend that you create separate workgroups for encrypted and non\-encrypted queries\. That way, you can enforce encryption for a workgroup that applies to all queries that run in it\. For more information, see [Encrypting Athena query results stored in Amazon S3Encrypting Athena query results when using JDBC or ODBC](encrypting-query-results-stored-in-s3.md)\.

1. Create workgroups as needed, and add tags to them\. For steps, see [Create a workgroup](workgroups-create-update-delete.md#creating-workgroups)\. 

1. Create IAM policies for your users, groups, or roles to enable their access to workgroups\. The policies establish the workgroup membership and access to actions on a `workgroup` resource\. For detailed steps, see [ IAM policies for accessing workgroups](workgroups-iam-policy.md)\. For example JSON policies, see [Access to workgroups and tags](workgroups-access.md)\.

1. Set workgroup settings\. Specify a location in Amazon S3 for query results and optionally specify the expected bucket owner, encryption settings, and control of objects written to the query results bucket\. You can enforce workgroup settings\. For more information, see [workgroup settings](workgroups-settings.md)\.
**Important**  
If you [override client\-side settings](workgroups-settings-override.md), Athena will use the workgroup's settings\. This affects queries that you run in the console, by using the drivers, the command line interface, or the API operations\.   
While queries continue to run, automation built based on availability of results in a certain Amazon S3 bucket may break\. We recommend that you inform your users before overriding\. After workgroup settings are set to override, you can omit specifying client\-side settings in the drivers or the API\.

1. Notify users which workgroups to use for running queries\. Send an email to inform your account's users about workgroup names that they can use, the required IAM policies, and the workgroup settings\.

1. Configure cost control limits, also known as data usage control limits, for queries and workgroups\. To notify you when a threshold is breached, create an Amazon SNS topic and configure subscriptions\. For detailed steps, see [Setting data usage control limits](workgroups-setting-control-limits-cloudwatch.md) and [Getting started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) in the *Amazon Simple Notification Service Getting Started Guide*\.

1. Switch to the workgroup so that you can run queries\.To run queries, switch to the appropriate workgroup\. For detailed steps, see [Specify a workgroup in which to run queries](workgroups-create-update-delete.md#specify-wkgroup-to-athena-in-which-to-run-queries)\.