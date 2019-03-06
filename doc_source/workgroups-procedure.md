# Setting up Workgroups<a name="workgroups-procedure"></a>

Setting up workgroups involves creating them and establishing permissions for their usage\. First, decide which workgroups your organization needs, and create them\. Next, set up IAM workgroup policies that control user access and actions on a `workgroup` resource\. Users with access to these workgroups can now run queries in them\.

**Note**  
Use these tasks for setting up workgroups when you begin to use them for the first time\. If your Athena account already uses workgroups, each account's user requires permissions to run queries in one or more workgroups in the account\. Before you run queries, check your IAM policy to see which workgroups you can access, adjust your policy if needed, and [switch](workgroups-create-update-delete.md#switching-workgroups) to a workgroup you intend to use\.

By default, if you have not created any workgroups, all queries in your account run in the primary workgroup:

![\[The screenshot of the primary workgroup being used.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-primary.png)


**High\-Level Procedure for Setting up Workgroups**  

| Step | Description | 
| --- | --- | 
| Step 1: Decide which workgroups to create\. |  For example, you can decide the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/workgroups-procedure.html)  | 
|  Step 2: Create workgroups as needed, and add tags to them\.   |  Open the Athena console, choose the **Workgroup:<workgroup\_name>** tab, and then choose **Create workgroup**\. For detailed steps, see [Create a Workgroup](workgroups-create-update-delete.md#creating-workgroups)\.   | 
|  Step 3: Create IAM policies for your users, groups, or roles to enable their access to workgroups\.   |  The policies establish the workgroup membership and access to actions on a `workgroup` resource\. For detailed steps, see [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\. For example JSON policies, see [Workgroup Example Policies](workgroups-access.md)\.  | 
|  Step 4: Set workgroup settings\. Specify a location in Amazon S3 for query results and encryption settings, if needed\. You can enforce workgroup settings\.   |  For more information, see [workgroup settings](workgroups-settings.md)\.  If you [override client\-side settings](workgroups-settings-override.md), Athena will use the workgroup's settings\. This affects queries that you run in the console, by using the drivers, the command line interface, or the API operations\.  While queries continue to run, automation built based on availability of results in a certain Amazon S3 bucket may break\. We recommend that you inform your users before overriding\. After workgroup settings are set to override, you can omit specifying client\-side settings in the drivers or the API\.   | 
| Step 5: Notify users which workgroups to use for running queries\. |  Inform your account's users about workgroup names that they can use, the required IAM policies, and the workgroup settings\.  | 
|  Step 6: Configure cost control limits, also known as data usage control limits, for queries and workgroups\.  To notify you when a threshold is breached, create an Amazon SNS topic and configure subscriptions\.   |  For detailed steps, see [Setting Data Usage Control Limits](workgroups-setting-control-limits-cloudwatch.md) and [Creating an Amazon SNS Topic](https://docs.aws.amazon.com/sns/latest/gsg/creating-an-sns-topic.html) in the *Amazon Simple Notification Service Getting Started Guide*\.  | 
|  Step 7: Switch to the workgroup so that you can run queries\.  | To run queries, switch to the appropriate workgroup\. For detailed steps, see [Specify a Workgroup in Which to Run Queries](workgroups-create-update-delete.md#specify-wkgroup-to-athena-in-which-to-run-queries)\. | 