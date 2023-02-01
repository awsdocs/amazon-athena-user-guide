# Setting up<a name="setting-up"></a>

If you've already signed up for Amazon Web Services, you can start using Amazon Athena immediately\. If you haven't signed up for AWS or need assistance getting started, be sure to complete the following tasks\.

## Grant programmatic access<a name="setting-up-grant-programmatic-access"></a>

Access keys consist of an access key ID and secret access key, which are used to sign programmatic requests that you make to AWS\. If you don't have access keys, you can create them from the AWS Management Console\. As a best practice, do not use the AWS account root user access keys for any task where it's not required\. Instead, [create a new administrator IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) with access keys for yourself\.

The only time that you can view or download the secret access key is when you create the keys\. You cannot recover them later\. However, you can create new access keys at any time\. You must also have permissions to perform the required IAM actions\. For more information, see [Permissions required to access IAM resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions-required.html) in the *IAM User Guide*\.

**To create access keys for an IAM user**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose the name of the user whose access keys you want to create, and then choose the **Security credentials** tab\.

1. In the **Access keys** section, choose **Create access key**\.

1. To view the new access key pair, choose **Show**\. You will not have access to the secret access key again after this dialog box closes\. Your credentials will look something like this:
   + Access key ID: AKIAIOSFODNN7EXAMPLE
   + Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

1. To download the key pair, choose **Download \.csv file**\. Store the keys in a secure location\. You will not have access to the secret access key again after this dialog box closes\.

   Keep the keys confidential in order to protect your AWS account and never email them\. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon\.com\. No one who legitimately represents Amazon will ever ask you for your secret key\.

1. After you download the `.csv` file, choose **Close**\. When you create an access key, the key pair is active by default, and you can use the pair right away\.

**Related topics**
+ [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*
+ [AWS security credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in *AWS General Reference* 

## Attach managed policies for Athena<a name="setting-up-attach-managed-policies-for-athena"></a>

Athena managed policies grant permissions to use Athena features\. You can attach these managed policies to one or more IAM roles that users can assume in order to use Athena\.

  An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an IAM identity that you can create in your account that has specific permissions\. An IAM role is similar to an IAM user in that it is an AWS identity with permissions policies that determine what the identity can and cannot do in AWS\. However, instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it\. Also, a role does not have standard long\-term credentials such as a password or access keys associated with it\. Instead, when you assume a role, it provides you with temporary security credentials for your role session\. 

For more information about roles, see [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) and [Creating IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html) in the *IAM User Guide*\. 

To create a role that grants access to Athena, you attach Athena managed policies to the role\. There are two managed policies for Athena: `AmazonAthenaFullAccess` and `AWSQuicksightAthenaAccess`\. These policies grant permissions to Athena to query Amazon S3 and to write the results of your queries to a separate bucket on your behalf\. To see the contents of these policies for Athena, see [AWS managed policies for Amazon Athena](managed-policies.md)\. 

For steps to attach the Athena managed policies to a role, follow [Adding IAM identity permissions \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html#add-policies-console) in the *IAM User Guide* and add the `AmazonAthenaFullAccess` and `AWSQuicksightAthenaAccess` managed policies to the role that you created\.

**Note**  
You may need additional permissions to access the underlying dataset in Amazon S3\. If you are not the account owner or otherwise have restricted access to a bucket, contact the bucket owner to grant access using a resource\-based bucket policy, or contact your account administrator to grant access using a role\-based policy\. For more information, see [Access to Amazon S3](s3-permissions.md)\. If the dataset or Athena query results are encrypted, you may need additional permissions\. For more information, see [Encryption at rest](encryption.md)\.