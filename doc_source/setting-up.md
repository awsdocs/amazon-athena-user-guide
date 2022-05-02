# Setting up<a name="setting-up"></a>

If you've already signed up for Amazon Web Services, you can start using Amazon Athena immediately\. If you haven't signed up for AWS or need assistance getting started, be sure to complete the following tasks:

[1\. Sign up for an AWS account](#setting-up-sign-up-for-aws)

[2\. Create an IAM administrator user and group](#setting-up-create-an-iam-user)

[3\. Attach managed policies for Athena](#setting-up-attach-managed-policies-for-athena)

[4\. Sign in as an IAM user](#setting-up-sign-in-as-an-iam-user)

## 1\. Sign up for an AWS account<a name="setting-up-sign-up-for-aws"></a>

When you sign up for AWS, your account is automatically signed up for all services in AWS, including Athena\. You are charged only for the services that you use\. For pricing information, see [Amazon Athena pricing](http://aws.amazon.com/athena/pricing/)\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [http://aws\.amazon\.com/](http://aws.amazon.com/), and then choose **Create an AWS account**\.

1. Follow the online instructions\. Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

1. Note your AWS account number, because you need it for the next task\.

## 2\. Create an IAM administrator user and group<a name="setting-up-create-an-iam-user"></a>

An AWS Identity and Access Management \(IAM\) user is an account that you create to access services\. It is a different user than your main AWS account\. As a security best practice, we recommend that you use the IAM user's credentials to access AWS services\. You use the IAM console to create an administrator IAM user and an **Administrators** group for the user\. You can then access the console for Athena and other AWS services by accessing a special link and providing the credentials for the IAM user that you created\.

For steps, see [Creating an administrator IAM user and user group \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html#getting-started_create-admin-group-console) in the *IAM User Guide*\.

## 3\. Attach managed policies for Athena<a name="setting-up-attach-managed-policies-for-athena"></a>

After you have created an IAM user, you must attach some Athena managed policies to the user so that the user can access Athena\. There are two managed policies for Athena: `AmazonAthenaFullAccess` and `AWSQuicksightAthenaAccess`\. These policies grant permissions to Athena to query Amazon S3 and to write the results of your queries to a separate bucket on your behalf\. To see the contents of these policies for Athena, see [AWS managed policies for Amazon Athena](managed-policies.md)\. 

For steps to attach the Athena managed policies, follow [Adding IAM identity permissions \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html#add-policies-console) in the *IAM User Guide* and add the `AmazonAthenaFullAccess` and `AWSQuicksightAthenaAccess` managed policies to the IAM administrator user that you created\.

**Note**  
You may need additional permissions to access the underlying dataset in Amazon S3\. If you are not the account owner or otherwise have restricted access to a bucket, contact the bucket owner to grant access using a resource\-based bucket policy, or contact your account administrator to grant access using an identity\-based policy\. For more information, see [Amazon S3 Permissions](s3-permissions.md)\. If the dataset or Athena query results are encrypted, you may need additional permissions\. For more information, see [Configuring Encryption Options](encryption.md)\.

## 4\. Sign in as an IAM user<a name="setting-up-sign-in-as-an-iam-user"></a>

To sign in as the new IAM user that you created, you can use the custom sign\-in URL for the IAM users of your account\. To see the sign\-in URL for the IAM users for your account, open the IAM console and choose **Users**, ***user\_name***, **Security credentials**, **Console sign\-in link**\. As a convenience, you can use the clipboard icon to copy the sign\-in URL to the clipboard\.

For more information about signing in as an IAM user, see [How IAM users sign in to your AWS account](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_how-users-sign-in.html) in the *IAM User Guide*\.