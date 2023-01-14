# Setting up<a name="setting-up"></a>

If you've already signed up for Amazon Web Services, you can start using Amazon Athena immediately\. If you haven't signed up for AWS or need assistance getting started, be sure to complete the following tasks\.

## Attach managed policies for Athena<a name="setting-up-attach-managed-policies-for-athena"></a>

Athena managed policies grant permissions to use Athena features\. You can attach these managed policies to one or more IAM roles that users can assume in order to use Athena\.

  An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an IAM identity that you can create in your account that has specific permissions\. An IAM role is similar to an IAM user in that it is an AWS identity with permissions policies that determine what the identity can and cannot do in AWS\. However, instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it\. Also, a role does not have standard long\-term credentials such as a password or access keys associated with it\. Instead, when you assume a role, it provides you with temporary security credentials for your role session\. 

For more information about roles, see [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) and [Creating IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html) in the *IAM User Guide*\. 

To create a role that grants access to Athena, you attach Athena managed policies to the role\. There are two managed policies for Athena: `AmazonAthenaFullAccess` and `AWSQuicksightAthenaAccess`\. These policies grant permissions to Athena to query Amazon S3 and to write the results of your queries to a separate bucket on your behalf\. To see the contents of these policies for Athena, see [AWS managed policies for Amazon Athena](managed-policies.md)\. 

For steps to attach the Athena managed policies to a role, follow [Adding IAM identity permissions \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html#add-policies-console) in the *IAM User Guide* and add the `AmazonAthenaFullAccess` and `AWSQuicksightAthenaAccess` managed policies to the role that you created\.

**Note**  
You may need additional permissions to access the underlying dataset in Amazon S3\. If you are not the account owner or otherwise have restricted access to a bucket, contact the bucket owner to grant access using a resource\-based bucket policy, or contact your account administrator to grant access using a role\-based policy\. For more information, see [Access to Amazon S3](s3-permissions.md)\. If the dataset or Athena query results are encrypted, you may need additional permissions\. For more information, see [Encryption at rest](encryption.md)\.