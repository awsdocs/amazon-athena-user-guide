# Managing Workgroups<a name="workgroups-create-update-delete"></a>

In the [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home), you can perform the following tasks:


| Statement | Description | 
| --- | --- | 
| [Create a Workgroup](#creating-workgroups) |  Create a new workgroup\.  | 
| [Edit a Workgroup](#editing-workgroups) | Edit a workgroup and change its settings\. You cannot change a workgroup's name, but you can create a new workgroup with the same settings and a different name\. | 
| [View the Workgroup's Details](#viewing-details-workgroups) | View the workgroup's details, such as its name, description, data usage limits, location of query results, and encryption\. You can also verify whether this workgroup enforces its settings, if Override client\-side settings is checked\. | 
| [Delete a Workgroup](#deleting-workgroups) |  Delete a workgroup\. If you delete a workgroup, query history, saved queries, the workgroup's settings and per\-query data limit controls are deleted\. The workgroup\-wide data limit controls remain in CloudWatch, and you can delete them individually\. The primary workgroup cannot be deleted\.  | 
| [Switch between Workgroups](#switching-workgroups) |  Switch between workgroups to which you have access\.   | 
| [Enable and Disable a Workgroup](#workgroups-enabled-disabled) |  Enable or disable a workgroup\. When a workgroup is disabled, its users cannot run queries, or create new named queries\. If you have access to it, you can still view metrics, data usage limit controls, workgroup's settings, query history, and saved queries\.  | 
| [Specify a Workgroup in Which to Run Queries](#specify-wkgroup-to-athena-in-which-to-run-queries) |  Before you can run queries, you must specify to Athena which workgroup to use\. You must have permissions to the workgroup\.   | 

## Create a Workgroup<a name="creating-workgroups"></a>

Creating a workgroup requires permissions to `CreateWorkgroup` API actions\. See [Access to Athena Workgroups](workgroups-access.md) and [IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\. If you are adding tags, you also need to add permissions to `TagResource`\. See [Tag Policy Examples](tags-access-control.md#tag-policy-examples)\.

**To create a workgroup in the console**

1. In the Athena console, choose the **Workgroup:<workgroup\_name>** tab\. A **Workgroups** panel displays\. 

1. In the **Workgroups** panel, choose **Create workgroup**\.   
![\[The screenshot that shows how to create a workgroup.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wkg-create.png)

1. In the **Create workgroup** dialog box, fill in the fields as follows:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/workgroups-create-update-delete.html)

1. Choose **Create workgroup**\. The workgroup appears in the list in the **Workgroups** panel\.

Alternatively, use the API operations to create a workgroup\. 

**Important**  
After you create workgroups, create [IAM Policies for Workgroups](workgroups-iam-policy.md) IAM that allow you to run workgroup\-related actions\. 

## Edit a Workgroup<a name="editing-workgroups"></a>

Editing a workgroup requires permissions to `UpdateWorkgroup` API operations\. See [Access to Athena Workgroups](workgroups-access.md) and [IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\. If you are adding or editing tags, you also need to have permissions to `TagResource`\. See [Tag Policy Examples](tags-access-control.md#tag-policy-examples)\.

**To edit a workgroup in the console**

1. In the Athena console, choose the **Workgroup:<workgroup\_name>** tab\. A **Workgroups** panel displays, listing all of the workgroups in the account\.   
![\[Workgroups list\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-list.png)

1. In the **Workgroups** panel, choose the workgroup that you want to edit\. The **View details** panel for the workgroup displays, with the **Overview** tab selected\. 

1. Choose **Edit workgroup**\.  
![\[Workgroup actions\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-edit-delete-enable-disable.png)

1. Change the fields as needed\. For the list of fields, see [Create workgroup](#creating-workgroups)\. You can change all fields except for the workgroup's name\. If you need to change the name, create another workgroup with the new name and the same settings\.

1. Choose **Save**\. The updated workgroup appears in the list in the **Workgroups** panel\.

## View the Workgroup's Details<a name="viewing-details-workgroups"></a>

For each workgroup, you can view its details\. The details include the workgroup's name, description, whether it is enabled or disabled, and the settings used for queries that run in the workgroup, which include the location of the query results and encryption configuration\. If a workgroup has data usage limits, they are also displayed\.

**To view the workgroup's details**
+ In the **Workgroups** panel, choose the workgroup that you want to edit\. The **View details** panel for the workgroup displays, with the **Overview** tab selected\. The workgroup details display, as in the following example:  
![\[The workgroup details screen.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-details-overview.png)

## Delete a Workgroup<a name="deleting-workgroups"></a>

You can delete a workgroup if you have permissions to do so\. The primary workgroup cannot be deleted\. 

If you have permissions, you can delete an empty workgroup at any time\. You can also delete a workgroup that contains saved queries\. In this case, before proceeding to delete a workgroup, Athena warns you that saved queries are deleted\.

If you delete a workgroup while you are in it, the console switches focus to the primary workgroup\. If you have access to it, you can run queries and view its settings\.

If you delete a workgroup, its settings and per\-query data limit controls are deleted\. The workgroup\-wide data limit controls remain in CloudWatch, and you can delete them there if needed\.

**Important**  
Before deleting a workgroup, ensure that its users also belong to other workgroups where they can continue to run queries\. If the users' IAM policies allowed them to run queries *only* in this workgroup, and you delete it, they no longer have permissions to run queries\. For more information, see [Example Policy for Running Queries in the Primary Workgroup](example-policies-workgroup.md#example4-run-in-primary-access)\.

**To delete a workgroup in the console**

1. In the Athena console, choose the **Workgroup:<workgroup\_name>** tab\. A **Workgroups** panel displays\.

1. In the **Workgroups** panel, choose the workgroup that you want to delete\. The **View details** panel for the workgroup displays, with the **Overview** tab selected\.

1. Choose **Delete workgroup**, and confirm the deletion\.

To delete a workgroup with the API operation, use the `DeleteWorkGroup` action\.

## Switch between Workgroups<a name="switching-workgroups"></a>

You can switch from one workgroup to another if you have permissions to both of them\.

You can open up to ten query tabs within each workgroup\. When you switch between workgroups, your query tabs remain open for up to three workgroups\. 

**To switch between workgroups**

1. In the Athena console, choose the **Workgroup:<workgroup\_name>** tab\. A **Workgroups** panel displays\. 

1. In the **Workgroups** panel, choose the workgroup that you want to switch to, and then choose **Switch workgroup**\.  
![\[Switching workgroups screenshot.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-switch.png)

1. Choose **Switch**\. The console shows the **Workgroup: <workgroup\_name>** tab with the name of the workgroup that you switched to\. You can now run queries in this workgroup\.

## Enable and Disable a Workgroup<a name="workgroups-enabled-disabled"></a>

If you have permissions to do so, you can enable or disable workgroups in the console, by using the API operations, or with the JDBC and ODBC drivers\.

**To enable or disable a workgroup**

1. In the Athena console, choose the **Workgroup:<workgroup\_name>** tab\. A **Workgroups** panel displays\. 

1. In the **Workgroups** panel, choose the workgroup, and then choose **Enable workgroup** or **Disable workgroup**\. If you disable a workgroup, its users cannot run queries in it, or create new named queries\. If you enable a workgroup, users can use it to run queries\.

## Specify a Workgroup in Which to Run Queries<a name="specify-wkgroup-to-athena-in-which-to-run-queries"></a>

Before you can run queries, you must specify to Athena which workgroup to use\. You need to have permissions to the workgroup\. 

**To specify a workgroup to Athena**

1. Make sure your permissions allow you to run queries in a workgroup that you intend to use\. For more information, see [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md)\.

1.  To specify the workgroup to Athena, use one of these options: 
   + If you are accessing Athena via the console, set the workgroup by [switching workgroups](#switching-workgroups)\.
   + If you are using the Athena API operations, specify the workgroup name in the API action\. For example, you can set the workgroup name in [StartQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_StartQueryExecution.html), as follows: 

     ```
     StartQueryExecutionRequest startQueryExecutionRequest = new StartQueryExecutionRequest()
                   .withQueryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
                   .withQueryExecutionContext(queryExecutionContext)
                  .withWorkgroup(WorkgroupName)
     ```
   + If you are using the JDBC or ODBC driver, set the workgroup name in the connection string using the `Workgroup` configuration parameter\. The driver passes the workgroup name to Athena\. Specify the workgroup parameter in the connection string as in the following example: 

     ```
     jdbc:awsathena://AwsRegion=<AWSREGION>;UID=<ACCESSKEY>;
     PWD=<SECRETKEY>;S3OutputLocation=s3://<athena-output>-<AWSREGION>/;
     Workgroup=<WORKGROUPNAME>;
     ```

     For more information, search for "Workgroup" in the driver documentation link included in [JDBC Driver Documentation](connect-with-jdbc.md#documentation-jdbc)\.