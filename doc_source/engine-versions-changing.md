# Changing Athena Engine Versions<a name="engine-versions-changing"></a>

Athena occasionally releases a new engine version to provide improved performance, functionality, and code fixes\. When a new engine version is available, Athena notifies you in the console\. You can choose to let Athena decide when to upgrade, or manually specify an Athena engine version per workgroup\.

**Topics**
+ [Finding the Query Engine Version for a Workgroup](#engine-versions-changing-finding-the-query-engine-version-for-a-workgroup)
+ [Changing the Engine Version](#engine-versions-changing-changing-the-engine-version)
+ [Specifying the Engine Version When You Create a Workgroup](#engine-versions-changing-specifying-the-engine-version-when-you-create-a-workgroup)
+ [Testing Queries in Advance of an Engine Version Upgrade](#engine-versions-testing)
+ [Troubleshooting Queries That Fail](#engine-versions-troubleshooting)

## Finding the Query Engine Version for a Workgroup<a name="engine-versions-changing-finding-the-query-engine-version-for-a-workgroup"></a>

The Athena console shows the engine version for the current workgroup at the bottom of the Query Editor window, as in the following example:

![\[The Athena engine version shown in the Query Editor.\]](http://docs.aws.amazon.com/athena/latest/ug/images/engine-versions-changing-1.png)

You can also use the **Workgroups** page to find the current engine version for any workgroup\.

**To find the current engine version for any workgroup**

1. In the Athena console, choose the **Workgroup** tab\.

1. In the list of workgroups, find the workgroup that you want\.

   The engine version is shown in the **Query engine version** column for the workgroup, as in the following example\.  
![\[The Workgroups page shows engine version for each workgroup.\]](http://docs.aws.amazon.com/athena/latest/ug/images/engine-versions-changing-2.png)

## Changing the Engine Version<a name="engine-versions-changing-changing-the-engine-version"></a>

When a new engine version is available, you can choose to let Athena decide when to upgrade the workgroup, or manually specify the Athena engine version that the workgroup uses\.

**To let Athena decide when to upgrade the workgroup**

1. In the Athena console, choose the **Workgroup** tab\.

1. In the list of workgroups, choose the workgroup that you want to configure\.

1. Choose **View details**\.

1. Choose **Edit workgroup**\.

1. Under **Query engine version**, for **Update query engine**, choose **Let Athena choose when to upgrade your workgroup**\. This is the default setting\.

1. Choose **Save**\.

   The workgroup's **Query engine update status** is set to **Pending automatic upgrade**\. When the update occurs, Athena will notify you in the Athena console and on your [AWS Personal Health Dashboard](https://aws.amazon.com/premiumsupport/technology/personal-health-dashboard/)\. The workgroup continues to use the current engine version until the update\.  
![\[A workgroup pending automatic upgrade.\]](http://docs.aws.amazon.com/athena/latest/ug/images/engine-versions-changing-3.png)

**To manually choose an engine version**

1. In the Athena console, choose the **Workgroup** tab\.

1. In the list of workgroups, choose the workgroup that you want to configure\.

1. Choose **View details**\.

1. Choose **Edit workgroup**\.

1. Under **Query engine version**, for **Update query engine**, choose **Manually choose an engine version now**\.

1. Choose the engine version that you want the workgroup to use, and then choose **Save**\.

1. If you chose an older engine version, a **Confirm Athena engine version** warning appears\. Read the warning, and then do one of the following:
   + To cancel the engine version change, choose **Cancel**\.
   + To confirm the engine version change, enter **confirm** in the box, and then choose **Confirm**\.

   The **Query engine update status** for the workgroup shows **Manually set**\.  
![\[A workgroup whose engine version has been manually set.\]](http://docs.aws.amazon.com/athena/latest/ug/images/engine-versions-changing-4.png)

## Specifying the Engine Version When You Create a Workgroup<a name="engine-versions-changing-specifying-the-engine-version-when-you-create-a-workgroup"></a>

When you create a workgroup, you can specify the engine version that the workgroup uses or let Athena decide when to upgrade the workgroup\. A best practice is to create a workgroup to test the new engine before you upgrade your other workgroups\.

**To specify the engine version when you create a workgroup**

1. In the Athena console, choose the **Workgroup** tab\.

1. On the **Workgroups** page, choose **Create workgroup**\.

1. On the **Create workgroup** page, under the **Query engine version**, for **Update query engine**, do one of the following:
   + Choose **Let Athena choose when to upgrade your workgroup**\. This is the default setting\.
   + Choose **Manually choose an engine version now**, and then choose an engine version\.

1. Enter information for the other fields as necessary\. For information about the other fields, see [Create a Workgroup](workgroups-create-update-delete.md#creating-workgroups)\.

1. Choose **Create workgroup**\.

## Testing Queries in Advance of an Engine Version Upgrade<a name="engine-versions-testing"></a>

When a workgroup is upgraded to a new engine version, some of your queries may break due to incompatibilities\. To make sure that your engine version upgrade goes smoothly, you can test your queries in advance\.

**To test your queries prior to an engine version upgrade**

1. Verify the engine version of the workgroup that you are using\. The engine version that you are using is displayed in the Athena Query Editor and on the **Workgroups** page\. For more information, see [Finding the Query Engine Version for a Workgroup](#engine-versions-changing-finding-the-query-engine-version-for-a-workgroup)\.

1. Create a test workgroup that uses the new engine version\. For more information, see [Specifying the Engine Version When You Create a Workgroup](#engine-versions-changing-specifying-the-engine-version-when-you-create-a-workgroup)\.

1. Use the new workgroup to run the queries that you want to test\.

1. If a query fails, use the [Athena Engine Version Reference](engine-versions-reference.md) to check for breaking changes that might be affecting the query\. Some changes may require you to update the syntax of your queries\.

1. If your queries still fail, contact AWS Support for assistance\. In the AWS Management Console, choose **Support**, **Support Center**, or visit the [Amazon Athena Forum](https://forums.aws.amazon.com/forum.jspa?forumID=242)\.

## Troubleshooting Queries That Fail<a name="engine-versions-troubleshooting"></a>

If a query fails after an engine version upgrade, use the [Athena Engine Version Reference](engine-versions-reference.md) to check for breaking changes, including changes that may affect the syntax in your queries\.

If your queries still fail, contact AWS Support for assistance\. In the AWS Management Console, choose **Support**, **Support Center**, or visit the [Amazon Athena Forum](https://forums.aws.amazon.com/forum.jspa?forumID=242)\.