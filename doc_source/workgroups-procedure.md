# Setting up Workgroups<a name="workgroups-procedure"></a>

Setting up workgroups involves creating them and establishing permissions for their usage\. First, decide which workgroups your organization needs, and create them\. Next, set up IAM workgroup policies that control user access and actions on a `workgroup` resource\. Users with access to these workgroups can now run queries in them\.

**Note**  
Use these tasks for setting up workgroups when you begin to use them for the first time\. If your Athena account already uses workgroups, each account's user requires permissions to run queries in one or more workgroups in the account\. Before you run queries, check your IAM policy to see which workgroups you can access, adjust your policy if needed, and [switch](workgroups-create-update-delete.md#switching-workgroups) to a workgroup you intend to use\.

By default, if you have not created any workgroups, all queries in your account run in the primary workgroup:

![\[The screenshot of the primary workgroup being used.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-primary.png)

Workgroups display in the Athena console in the **Workgroup:*<workgroup\_name>*** tab\. The console lists the workgroup that you have switched to\. When you run queries, they run in this workgroup\. You can run queries in the workgroup in the console, or by using the API operations, the command line interface, or a client application through the JDBC or ODBC driver\. When you have access to a workgroup, you can view workgroup's settings, metrics, and data usage control limits\. Additionally, you can have permissions to edit the settings and data usage control limits\.

**To Set Up Workgroups**

1.  Decide which workgroups to create\. For example, you can decide the following:
   + Who can run queries in each workgroup, and who owns workgroup configuration\. This determines IAM policies you create\. For more information, see [IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\.
   + Which locations in Amazon S3 to use for the query results for queries that run in each workgroup\. A location must exist in Amazon S3 *before* you can specify it for the workgroup query results\. All users who use a workgroup must have access to this location\. For more information, see [Workgroup Settings](workgroups-settings.md)\. 
   + Which encryption settings are required, and which workgroups have queries that must be encrypted\. We recommend that you create separate workgroups for encrypted and non\-encrypted queries\. That way, you can enforce encryption for a workgroup that applies to all queries that run in it\. For more information, see [Encrypting Query Results Stored in Amazon S3](encrypting-query-results-stored-in-s3.md)\.

1. Create workgroups as needed, and add tags to them\. Open the Athena console, choose the **Workgroup:<workgroup\_name>** tab, and then choose **Create workgroup**\. For detailed steps, see [Create a Workgroup](workgroups-create-update-delete.md#creating-workgroups)\. 

1. Create IAM policies for your users, groups, or roles to enable their access to workgroups\. The policies establish the workgroup membership and access to actions on a `workgroup` resource\. For detailed steps, see [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\. For example JSON policies, see [Workgroup Example Policies](workgroups-access.md)\.

1. Set workgroup settings\. Specify a location in Amazon S3 for query results and encryption settings, if needed\. You can enforce workgroup settings\. For more information, see [workgroup settings](workgroups-settings.md)\.
**Important**  
If you [override client\-side settings](workgroups-settings-override.md), Athena will use the workgroup's settings\. This affects queries that you run in the console, by using the drivers, the command line interface, or the API operations\.   
While queries continue to run, automation built based on availability of results in a certain Amazon S3 bucket may break\. We recommend that you inform your users before overriding\. After workgroup settings are set to override, you can omit specifying client\-side settings in the drivers or the API\.

1. Notify users which workgroups to use for running queries\. Send an email to inform your account's users about workgroup names that they can use, the required IAM policies, and the workgroup settings\.

1. Configure cost control limits, also known as data usage control limits, for queries and workgroups\. To notify you when a threshold is breached, create an Amazon SNS topic and configure subscriptions\. For detailed steps, see [Setting Data Usage Control Limits](workgroups-setting-control-limits-cloudwatch.md) and [Creating an Amazon SNS Topic](https://docs.aws.amazon.com/sns/latest/gsg/creating-an-sns-topic.html) in the *Amazon Simple Notification Service Getting Started Guide*\.

1. Switch to the workgroup so that you can run queries\.To run queries, switch to the appropriate workgroup\. For detailed steps, see [Specify a Workgroup in Which to Run Queries](workgroups-create-update-delete.md#specify-wkgroup-to-athena-in-which-to-run-queries)\.