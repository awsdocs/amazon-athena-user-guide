# Managed Policies for User Access<a name="managed-policies"></a>

To allow or deny Amazon Athena service actions for yourself or other users using AWS Identity and Access Management \(IAM\), you attach identity\-based policies to principals, such as users or groups\. 

Each identity\-based policy consists of statements that define the actions that are allowed or denied\. For more information and step\-by\-step instructions for attaching a policy to a user, see [Attaching Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console) in the *IAM User Guide*\. For a list of actions, see the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\.

*Managed* policies are easy to use and are updated automatically with the required actions as the service evolves\.

Athena has these managed policies:
+ The `AmazonAthenaFullAccess` managed policy grants full access to Athena\. Attach it to users and other principals who need full access to Athena\. See [AmazonAthenaFullAccess Managed Policy](amazonathenafullaccess-managed-policy.md)\.
+ The `AWSQuicksightAthenaAccess` managed policy grants access to actions that Amazon QuickSightneeds to integrate with Athena\. Attach this policy to principals who use Amazon QuickSight in conjunction with Athena\. See [AWSQuicksightAthenaAccess Managed Policy](awsquicksightathenaaccess-managed-policy.md)\.

 *Customer\-managed* and *inline* identity\-based policies allow you to specify more detailed Athena actions within a policy to fine\-tune access\. We recommend that you use the `AmazonAthenaFullAccess` policy as a starting point and then allow or deny specific actions listed in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. For more information about inline policies, see [Managed Policies and Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) in the *IAM User Guide*\.

If you also have principals that connect using JDBC, you must provide the JDBC driver credentials to your application\. For more information, see [Service Actions for JDBC Connections](policy-actions.md)\.

If you use AWS Glue with Athena, and have encrypted the AWS Glue Data Catalog, you must specify additional actions in the identity\-based IAM policies for Athena\. For more information, see [Access to Encrypted Metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.

**Important**  
If you create and use workgroups, make sure your policies include appropriate access to workgroup actions\. For detailed information, see [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md) and [Workgroup Example Policies](example-policies-workgroup.md)\. 