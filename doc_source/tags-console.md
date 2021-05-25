# Working with Tags on Workgroups in the Console<a name="tags-console"></a>

Using the Athena console, you can see which tags are in use by each workgroup in your account\. You can view tags by workgroup only\. You can also use the Athena console to apply, edit, or remove tags from one workgroup at a time\.

You can search workgroups using the tags you created\.

**Topics**
+ [Displaying Tags for Individual Workgroups](#tags-display)
+ [Adding and Deleting Tags on an Individual Workgroup](#tags-add-delete)

## Displaying Tags for Individual Workgroups<a name="tags-display"></a>

You can display tags for an individual workgroup in the Athena console\. 

To view a list of tags for a workgroup, select the workgroup, choose **View Details**, and then choose the **Tags** tab\. The list of tags for the workgroup displays\. You can also view tags on a workgroup if you choose **Edit Workgroup**\.

To search for tags, choose the **Tags** tab, and then enter a tag name into the search tool\.

## Adding and Deleting Tags on an Individual Workgroup<a name="tags-add-delete"></a>

You can manage tags for an individual workgroup directly from the **Workgroups** tab\.

**Note**  
If you want users to add tags when they create a workgroup in the console or pass in tags when they use the CreateWorkGroup action, make sure that you give the users IAM permissions to the TagResource and CreateWorkGroup actions\.



**To add a tag when creating a new workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. On the navigation menu, choose the **Workgroups** tab\.

1. Choose **Create workgroup** and fill in the values, as needed\. For detailed steps, see [Create a Workgroup](workgroups-create-update-delete.md#creating-workgroups)\.

1. Add one or more tags, by specifying keys and values\. Do not add duplicate tag keys at the same time to the same workgroup\. If you do, Athena issues an error message\. For more information, see [Tag Restrictions](tags.md#tag-restrictions)\.

1. When you are done, choose **Create Workgroup**\.

**To add or edit a tag to an existing workgroup**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home), choose the **Workgroups** tab, and select the workgroup\.

1. Choose **View details**\.

1. Do one of the following:
   + Choose the **Tags** tab, and then choose **Manage tags**\. 
   + Choose **Edit workgroup**, and then scroll down to the **Tags** section\.

1. Specify the key and value for each tag\. For more information, see [Tag Restrictions](tags.md#tag-restrictions)\.

1. Choose **Save**\.

**To delete a tag from an individual workgroup**

1. Open the Athena console, and then choose the **Workgroups** tab\.

1. In the workgroup list, select the workgroup, and then choose **View details**\.

1. Do one of the following:
   + Choose the **Tags** tab, and then choose **Manage tags**\. 
   + Choose **Edit workgroup**, and then scroll down to the **Tags** section\.

1. In the list of tags, select the **delete** button \(**x**\) for the tag that you want to delete, and then choose **Save**\.