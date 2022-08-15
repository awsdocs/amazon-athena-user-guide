# IAM policies for accessing data catalogs<a name="datacatalogs-iam-policy"></a>

To control access to data catalogs, use resource\-level IAM permissions or identity\-based IAM policies\. 

The following procedure is specific to Athena\. 

For IAM\-specific information, see the links listed at the end of this section\. For information about example JSON data catalog policies, see [Data Catalog example policies](datacatalogs-example-policies.md)\.

**To use the visual editor in the IAM console to create a data catalog policy**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane on the left, choose **Policies**, and then choose **Create policy**\.

1. On the **Visual editor** tab, choose **Choose a service**\. Then choose Athena to add to the policy\.

1. Choose **Select actions**, and then choose the actions to add to the policy\. The visual editor shows the actions available in Athena\. For more information, see [Actions, resources, and condition keys for Amazon Athena](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonathena.html) in the *Service Authorization Reference*\.

1. Choose **add actions** to type a specific action or use wildcards \(\*\) to specify multiple actions\. 

   By default, the policy that you are creating allows the actions that you choose\. If you chose one or more actions that support resource\-level permissions to the `datacatalog` resource in Athena, then the editor lists the `datacatalog` resource\. 

1. Choose **Resources** to specify the specific data catalogs for your policy\. For example JSON data catalog policies, see [Data Catalog example policies](datacatalogs-example-policies.md)\.

1. Specify the `datacatalog` resource as follows:

   ```
   arn:aws:athena:<region>:<user-account>:datacatalog/<datacatalog-name>
   ```

1. Choose **Review policy**, and then type a **Name** and a **Description** \(optional\) for the policy that you are creating\. Review the policy summary to make sure that you granted the intended permissions\. 

1. Choose **Create policy** to save your new policy\.

1. Attach this identity\-based policy to a user, a group, or role and specify the `datacatalog` resources they can access\.

For more information, see the following topics in the *Service Authorization Reference* and the *IAM User Guide*:
+ [Actions, resources, and condition keys for Amazon Athena](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonathena.html)
+ [Creating policies with the visual editor](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-visual-editor)
+ [Adding and removing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)
+ [Controlling access to resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html#access_controlling-resources)

For example JSON data catalog policies, see [Data Catalog example policies](datacatalogs-example-policies.md)\.

For information about AWS Glue permissions and AWS Glue crawler permissions, see [Setting up IAM permissions for AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/getting-started-access.html) and [Crawler prerequisites](https://docs.aws.amazon.com/glue/latest/dg/crawler-prereqs.html) in the *AWS Glue Developer Guide*\.

For a complete list of Amazon Athena actions, see the API action names in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. 