# Managing Workgroups<a name="workgroups-create-update-delete"></a>

In the [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home), you can perform the following tasks:


| Statement | Description | 
| --- | --- | 
|  [Create a Workgroup](#creating-workgroups)  |  Create a new workgroup\.  | 
|  [Edit a Workgroup](#editing-workgroups)  | Edit a workgroup and change its settings\. You cannot change a workgroup's name, but you can create a new workgroup with the same settings and a different name\. | 
|  [View the Workgroup's Details](#viewing-details-workgroups)  | View the workgroup's details, such as its name, description, data usage limits, location of query results, expected query results bucket owner, and encryption\. You can also verify whether this workgroup enforces its settings, if Override client\-side settings is checked\. | 
|  [Delete a Workgroup](#deleting-workgroups)  |  Delete a workgroup\. If you delete a workgroup, query history, saved queries, the workgroup's settings and per\-query data limit controls are deleted\. The workgroup\-wide data limit controls remain in CloudWatch, and you can delete them individually\. The primary workgroup cannot be deleted\.  | 
|  [Switch Workgroups](#switching-workgroups)  |  Switch between workgroups to which you have access\.   | 
|  [Copy a Saved Query between Workgroups](#copy-a-query-between-workgroups)  | Copy a saved query between workgroups\. You might want to do this if, for example, you created a query in a preview workgroup and you want to make it available in a nonpreview workgroup\. | 
|  [Enable and Disable a Workgroup](#workgroups-enabled-disabled)  |  Enable or disable a workgroup\. When a workgroup is disabled, its users cannot run queries, or create new named queries\. If you have access to it, you can still view metrics, data usage limit controls, workgroup's settings, query history, and saved queries\.  | 
|  [Specify a Workgroup in Which to Run Queries](#specify-wkgroup-to-athena-in-which-to-run-queries)  |  Before you can run queries, you must specify to Athena which workgroup to use\. You must have permissions to the workgroup\.   | 

## Create a Workgroup<a name="creating-workgroups"></a>

Creating a workgroup requires permissions to `CreateWorkgroup` API actions\. See [Access to Athena Workgroups](workgroups-access.md) and [IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\. If you are adding tags, you also need to add permissions to `TagResource`\. See [Tag Policy Examples for Workgroups](tags-access-control.md#tag-policy-examples-workgroups)\.

**To create a workgroup in the console**

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the Athena console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, choose **Create workgroup**\. 

1. On the **Create workgroup** page, fill in the fields as follows:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/workgroups-create-update-delete.html)

1. Choose **Create workgroup**\. The workgroup appears in the list on the **Workgroups** page\.

You can also use the [CreateWorkGroup](https://docs.aws.amazon.com/athena/latest/APIReference/API_CreateWorkGroup.html) API operation to create a workgroup\. 

**Important**  
After you create workgroups, create [IAM Policies for Workgroups](workgroups-iam-policy.md) IAM that allow you to run workgroup\-related actions\. 

## Edit a Workgroup<a name="editing-workgroups"></a>

Editing a workgroup requires permissions to `UpdateWorkgroup` API operations\. See [Access to Athena Workgroups](workgroups-access.md) and [IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\. If you are adding or editing tags, you also need to have permissions to `TagResource`\. See [Tag Policy Examples for Workgroups](tags-access-control.md#tag-policy-examples-workgroups)\.

**To edit a workgroup in the console**

1. In the Athena console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, select the button for the workgroup that you want to edit\. 

1. Choose **Actions**, **Edit**\.

1. Change the fields as needed\. For the list of fields, see [Create workgroup](#creating-workgroups)\. You can change all fields except for the workgroup's name\. If you need to change the name, create another workgroup with the new name and the same settings\.

1. Choose **Save changes**\. The updated workgroup appears in the list on the **Workgroups** page\.

## View the Workgroup's Details<a name="viewing-details-workgroups"></a>

For each workgroup, you can view its details\. The details include the workgroup's name, description, whether it is enabled or disabled, and the settings used for queries that run in the workgroup, which include the location of the query results and encryption configuration\. If a workgroup has data usage limits, they are also displayed\.

**To view the workgroup's details**

1. In the Athena console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, choose the link of the workgroup that you want to view\. The **Overview Details** page for the workgroup displays\.

## Delete a Workgroup<a name="deleting-workgroups"></a>

You can delete a workgroup if you have permissions to do so\. The primary workgroup cannot be deleted\. 

If you have permissions, you can delete an empty workgroup at any time\. You can also delete a workgroup that contains saved queries\. In this case, before proceeding to delete a workgroup, Athena warns you that saved queries are deleted\.

If you delete a workgroup while you are in it, the console switches focus to the primary workgroup\. If you have access to it, you can run queries and view its settings\.

If you delete a workgroup, its settings and per\-query data limit controls are deleted\. The workgroup\-wide data limit controls remain in CloudWatch, and you can delete them there if needed\.

**Important**  
Before deleting a workgroup, ensure that its users also belong to other workgroups where they can continue to run queries\. If the users' IAM policies allowed them to run queries *only* in this workgroup, and you delete it, they no longer have permissions to run queries\. For more information, see [Example Policy for Running Queries in the Primary Workgroup](example-policies-workgroup.md#example4-run-in-primary-access)\.

**To delete a workgroup in the console**

1. In the Athena console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, select the button for the workgroup that you want to delete\.

1. Choose **Actions**, **Delete**\.

1. At the **Delete workgroup** confirmation prompt, enter the name of the workgroup, and then choose **Delete**\.

To delete a workgroup with the API operation, use the `DeleteWorkGroup` action\.

## Switch Workgroups<a name="switching-workgroups"></a>

You can switch from one workgroup to another if you have permissions to both of them\.

You can open up to ten query tabs within each workgroup\. When you switch between workgroups, your query tabs remain open for up to three workgroups\. 

**To switch workgroups**

1. In the Athena console, use the **Workgroup** option on the upper right to choose a workgroup\. 

1. If the **Workgroup *workgroup\-name* settings** dialog box appears, choose **Acknowledge**\.

The **Workgroup** option shows the name of the workgroup that you switched to\. You can now run queries in this workgroup\.

## Copy a Saved Query between Workgroups<a name="copy-a-query-between-workgroups"></a>

Currently, the Athena console does not have an option to to copy a saved query from one workgroup to another directly, but you can perform the same task manually by using the following procedure\.

**To copy a saved query between workgroups**

1. In the Athena console, from the workgroup that you want to copy the query from, choose the **Saved queries** tab\. 

1. Choose the link of the saved query that you want to copy\. Athena opens the query in the query editor\.

1. In the query editor, select the query text, and then press **Ctrl\+C** to copy it\.

1. [Switch](#switching-workgroups) to the destination workgroup, or [create a workgroup](#creating-workgroups), and then switch to it\.

1. Open a new tab in the query editor, and then press **Ctrl\+V** to paste the text into the new tab\.

1. In the query editor, choose **Save as** to save the query in the destination workgroup\.

1. In the **Choose a name** dialog box, enter a name for the query and an optional description\.

1. Choose **Save**\.

## Enable and Disable a Workgroup<a name="workgroups-enabled-disabled"></a>

If you have permissions to do so, you can enable or disable workgroups in the console, by using the API operations, or with the JDBC and ODBC drivers\.

**To enable or disable a workgroup**

1. In the Athena console navigation pane, choose **Workgroups**\.

1. On the **Workgroups** page, choose the link for the workgroup\. 

1. On the upper right, choose **Enable workgroup** or **Disable workgroup**\.

1. At the confirmation prompt, choose **Enable** or **Disable**\. If you disable a workgroup, its users cannot run queries in it, or create new named queries\. If you enable a workgroup, users can use it to run queries\.

## Specify a Workgroup in Which to Run Queries<a name="specify-wkgroup-to-athena-in-which-to-run-queries"></a>

To specify a workgroup to use, you must have permissions to the workgroup\. 

**To specify the workgroup to use**

1. Make sure your permissions allow you to run queries in a workgroup that you intend to use\. For more information, see [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\.

1.  To specify the workgroup, use one of these options: 
   + If you are using the Athena console, set the workgroup by [switching workgroups](#switching-workgroups)\.
   + If you are using the Athena API operations, specify the workgroup name in the API action\. For example, you can set the workgroup name in [StartQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_StartQueryExecution.html), as follows: 

     ```
     StartQueryExecutionRequest startQueryExecutionRequest = new StartQueryExecutionRequest()
                   .withQueryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
                   .withQueryExecutionContext(queryExecutionContext)
                   .withWorkGroup(WorkgroupName)
     ```
   + If you are using the JDBC or ODBC driver, set the workgroup name in the connection string using the `Workgroup` configuration parameter\. The driver passes the workgroup name to Athena\. Specify the workgroup parameter in the connection string as in the following example: 

     ```
     jdbc:awsathena://AwsRegion=<AWSREGION>;UID=<ACCESSKEY>;
     PWD=<SECRETKEY>;S3OutputLocation=s3://<athena-output>-<AWSREGION>/;
     Workgroup=<WORKGROUPNAME>;
     ```

     For more information, search for "Workgroup" in the driver documentation link included in [JDBC Driver Documentation](connect-with-jdbc.md#documentation-jdbc)\.