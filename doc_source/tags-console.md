# Working with tags on workgroups in the console<a name="tags-console"></a>

Using the Athena console, you can see which tags are in use by each workgroup in your account\. You can view tags by workgroup only\. You can also use the Athena console to apply, edit, or remove tags from one workgroup at a time\.

You can search workgroups using the tags you created\.

**Topics**
+ [Displaying tags for individual workgroups](#tags-display)
+ [Adding and deleting tags on an individual workgroup](#tags-add-delete)

## Displaying tags for individual workgroups<a name="tags-display"></a>

**To display tags for an individual workgroup in the Athena console**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. On the navigation menu, choose **Workgroups**, and then choose the workgroup that you want\.

1. Do one of the following:
   + Choose the **Tags** tab\. If the list of tags is long, use the search box\.
   + Choose **Edit**, and then scroll down to the **Tags** section\.

## Adding and deleting tags on an individual workgroup<a name="tags-add-delete"></a>

You can manage tags for an individual workgroup directly from the **Workgroups** tab\.

**Note**  
If you want users to add tags when they create a workgroup in the console or pass in tags when they use the CreateWorkGroup action, make sure that you give the users IAM permissions to the TagResource and CreateWorkGroup actions\.

**To add a tag when you create a new workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. On the navigation menu, choose **Workgroups**\.

1. Choose **Create workgroup** and fill in the values as needed\. For detailed steps, see [Create a workgroup](workgroups-create-update-delete.md#creating-workgroups)\.

1. In the **Tags** section, add one or more tags by specifying keys and values\. Do not add duplicate tag keys at the same time to the same workgroup\. If you do, Athena issues an error message\. For more information, see [Tag restrictions](tags.md#tag-restrictions)\.

1. When you are done, choose **Create workgroup**\.

**To add or edit a tag to an existing workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. In the navigation pane, choose **Workgroups**\.

1. Choose the workgroup that you want to modify\.

1. Do one of the following:
   + Choose the **Tags** tab, and then choose **Manage tags**\. 
   + Choose **Edit**, and then scroll down to the **Tags** section\.

1. Specify a key and value for each tag\. For more information, see [Tag restrictions](tags.md#tag-restrictions)\.

1. Choose **Save**\.

**To delete a tag from an individual workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. In the navigation pane, choose **Workgroups**\.

1. Choose the workgroup that you want to modify\.

1. Do one of the following:
   + Choose the **Tags** tab, and then choose **Manage tags**\. 
   + Choose **Edit**, and then scroll down to the **Tags** section\.

1. In the list of tags, choose **Remove** for the tag that you want to delete, and then choose **Save**\.