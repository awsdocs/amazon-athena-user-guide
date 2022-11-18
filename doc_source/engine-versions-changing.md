# Changing Athena engine versions<a name="engine-versions-changing"></a>

Athena occasionally releases a new engine version to provide improved performance, functionality, and code fixes\. When a new engine version is available, Athena notifies you in the console\. You can choose to let Athena decide when to upgrade, or manually specify an Athena engine version per workgroup\.

**Topics**
+ [Finding the query engine version for a workgroup](#engine-versions-changing-finding-the-query-engine-version-for-a-workgroup)
+ [Changing the engine version using the Athena console](#engine-versions-changing-changing-the-engine-version)
+ [Changing the engine version using the AWS CLI](#engine-versions-changing-changing-the-engine-version-cli)
+ [Specifying the engine version when you create a workgroup](#engine-versions-changing-specifying-the-engine-version-when-you-create-a-workgroup)
+ [Testing queries in advance of an engine version upgrade](#engine-versions-testing)
+ [Troubleshooting queries that fail](#engine-versions-troubleshooting)

## Finding the query engine version for a workgroup<a name="engine-versions-changing-finding-the-query-engine-version-for-a-workgroup"></a>

You can use the **Workgroups** page to find the current engine version for any workgroup\.

**To find the current engine version for any workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the Athena console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, find the workgroup that you want\. The **Query engine version** column for the workgroup displays the query engine version\.

## Changing the engine version using the Athena console<a name="engine-versions-changing-changing-the-engine-version"></a>

When a new engine version is available, you can choose to let Athena decide when to upgrade the workgroup, or manually specify the Athena engine version that the workgroup uses\. If only one version is currently available, manually specifying a different version is not possible\.

**Note**  
To change the engine version for a workgroup, you must have permission to perform the `athena:ListEngineVersions` action on the workgroup\. For IAM policy examples, see [Workgroup example policies](example-policies-workgroup.md)\. 

**To let Athena decide when to upgrade the workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. In the console navigation pane, choose **Workgroups**\.

1. In the list of workgroups, choose the link for the workgroup that you want to configure\.

1. Choose **Edit**\.

1. In the **Query engine version** section, for **Update query engine**, choose **Automatic** to let Athena choose when to upgrade your workgroup\. This is the default setting\.

1. Choose **Save changes**\.

   In the list of workgroups, the **Query engine update status** for the workgroup shows **Automatic**\.

**To manually choose an engine version**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. In the console navigation pane, choose **Workgroups**\.

1. In the list of workgroups, choose the link for the workgroup that you want to configure\.

1. Choose **Edit**\.

1. In the **Query engine version** section, for **Update query engine**, choose **Manual** to manually choose an engine version\.

1. Use the **Query engine version** option to choose the engine version that you want the workgroup to use\. If a different engine version is unavailable, a different engine version cannot be specified\.

1. Choose **Save changes**\.

   In the list of workgroups, the **Query engine update status** for the workgroup shows **Manual**\.

## Changing the engine version using the AWS CLI<a name="engine-versions-changing-changing-the-engine-version-cli"></a>

To change the engine version using the AWS CLI, use the syntax in the following example\.

```
aws athena update-work-group --work-group workgroup-name --configuration-updates EngineVersion={SelectedEngineVersion='Athena engine version 3'}
```

## Specifying the engine version when you create a workgroup<a name="engine-versions-changing-specifying-the-engine-version-when-you-create-a-workgroup"></a>

When you create a workgroup, you can specify the engine version that the workgroup uses or let Athena decide when to upgrade the workgroup\. If a new engine version is available, a best practice is to create a workgroup to test the new engine before you upgrade your other workgroups\. To specify the engine version for a workgroup, you must have the `athena:ListEngineVersions` permission on the workgroup\. For IAM policy examples, see [Workgroup example policies](example-policies-workgroup.md)\.

**To specify the engine version when you create a workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. In the console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, choose **Create workgroup**\.

1. On the **Create workgroup** page, in the **Query engine version** section, do one of the following:
   + Choose **Automatic** to let Athena choose when to upgrade your workgroup\. This is the default setting\.
   + Choose **Manual** to manually choose a different engine version if one is available\.

1. Enter information for the other fields as necessary\. For information about the other fields, see [Create a workgroup](workgroups-create-update-delete.md#creating-workgroups)\.

1. Choose **Create workgroup**\.

## Testing queries in advance of an engine version upgrade<a name="engine-versions-testing"></a>

When a workgroup is upgraded to a new engine version, some of your queries can break due to incompatibilities\. To make sure that your engine version upgrade goes smoothly, you can test your queries in advance\.

**To test your queries prior to an engine version upgrade**

1. Verify the engine version of the workgroup that you are using\. The engine version that you are using is displayed on the **Workgroups** page in the **Query engine version** column for for the workgroup\. For more information, see [Finding the query engine version for a workgroup](#engine-versions-changing-finding-the-query-engine-version-for-a-workgroup)\.

1. Create a test workgroup that uses the new engine version\. For more information, see [Specifying the engine version when you create a workgroup](#engine-versions-changing-specifying-the-engine-version-when-you-create-a-workgroup)\.

1. Use the new workgroup to run the queries that you want to test\.

1. If a query fails, use the [Athena engine version reference](engine-versions-reference.md) to check for breaking changes that might be affecting the query\. Some changes may require you to update the syntax of your queries\.

1. If your queries still fail, contact AWS Support for assistance\. In the AWS Management Console, choose **Support**, **Support Center**, or ask a question on [AWS re:Post](https://repost.aws/tags/TA78iVOM7gR62_QqDe2-CmiA/amazon-athena) using the **Amazon Athena** tag\.

## Troubleshooting queries that fail<a name="engine-versions-troubleshooting"></a>

If a query fails after an engine version upgrade, use the [Athena engine version reference](engine-versions-reference.md) to check for breaking changes, including changes that may affect the syntax in your queries\.

If your queries still fail, contact AWS Support for assistance\. In the AWS Management Console, choose **Support**, **Support Center**, or ask a question on [AWS re:Post](https://repost.aws/tags/TA78iVOM7gR62_QqDe2-CmiA/amazon-athena) using the **Amazon Athena** tag\.