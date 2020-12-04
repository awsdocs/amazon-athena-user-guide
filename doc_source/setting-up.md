# Setting Up<a name="setting-up"></a>

If you've already signed up for Amazon Web Services \(AWS\), you can start using Amazon Athena immediately\. If you haven't signed up for AWS, or if you need assistance querying data using Athena, first complete the tasks below:

## Sign Up for AWS<a name="sign-up-for-aws"></a>

When you sign up for AWS, your account is automatically signed up for all services in AWS, including Athena\. You are charged only for the services that you use\. When you use Athena, you use Amazon S3 to store your data\. Athena has no AWS Free Tier pricing\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

### To create an AWS account<a name="to-create-an-aws-account"></a>

1. Open [http://aws\.amazon\.com/](http://aws.amazon.com/), and then choose **Create an AWS Account**\.

1. Follow the online instructions\. Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

Note your AWS account number, because you need it for the next task\.

## Create an IAM User<a name="create-an-iam-user"></a>

An AWS Identity and Access Management \(IAM\) user is an account that you create to access services\. It is a different user than your main AWS account\. As a security best practice, we recommend that you use the IAM user's credentials to access AWS services\. Create an IAM user, and then add the user to an IAM group that has administrative permissions or grant the user administrative permissions\. You can then access AWS using a special URL and the credentials for the IAM user\.

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM console\. If you aren't familiar with using the console, see [Working with the AWS Management Console](https://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/getting-started.html)\.

### To create a group for administrators<a name="to-create-a-group-for-administrators"></a>

1. Sign in to the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Groups**, **Create New Group**\.

1. For **Group Name**, type a name for your group, such as `Administrators`, and choose **Next Step**\.

1. In the list of policies, select the check box next to the **AdministratorAccess** policy\. You can use the **Filter** menu and the **Search** field to filter the list of policies\.

1. Choose **Next Step**, **Create Group**\. Your new group is listed under **Group Name**\.

### To create an IAM user for yourself, add the user to the administrators group, and create a password for the user<a name="to-create-an-iam-user-for-yourself-add-the-user-to-the-administrators-group-and-create-a-password-for-the-user"></a>

1. In the navigation pane, choose **Users**, and then **Create New Users**\.

1. For **1**, type a user name\.

1. Clear the check box next to **Generate an access key for each user** and then **Create**\.

1. In the list of users, select the name \(not the check box\) of the user you just created\. You can use the **Search** field to search for the user name\.

1. Choose **Groups**, **Add User to Groups**\.

1. Select the check box next to the administrators and choose **Add to Groups**\.

1. Choose the **Security Credentials** tab\. Under **Sign\-In Credentials**, choose **Manage Password**\.

1. Choose **Assign a custom password**\. Then type a password in the **Password** and **Confirm Password** fields\. When you are finished, choose **Apply**\.

1. To sign in as this new IAM user, sign out of the AWS console, then use the following URL, where `your_aws_account_id` is your AWS account number without the hyphens \(for example, if your AWS account number is 1234\-5678\-9012, your AWS account ID is 123456789012\):

```
https://*your_account_alias*.signin.aws.amazon.com/console/
```

It is also possible the sign\-in link will use your account name instead of number\. To verify the sign\-in link for IAM users for your account, open the IAM console and check under **IAM users sign\-in link** on the dashboard\.

## Attach Managed Policies for Using Athena<a name="attach-managed-policies-for-using-ate"></a>

Attach Athena managed policies to the IAM account you use to access Athena\. There are two managed policies for Athena: `AmazonAthenaFullAccess` and `AWSQuicksightAthenaAccess`\. These policies grant permissions to Athena to query Amazon S3 as well as write the results of your queries to a separate bucket on your behalf\. For more information and step\-by\-step instructions, see [Adding IAM Identity Permissions \(Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html#add-policies-console) in the *AWS Identity and Access Management User Guide*\. For information about policy contents, see [IAM Policies for User Access](managed-policies.md)\.

**Note**  
You may need additional permissions to access the underlying dataset in Amazon S3\. If you are not the account owner or otherwise have restricted access to a bucket, contact the bucket owner to grant access using a resource\-based bucket policy, or contact your account administrator to grant access using an identity\-based policy\. For more information, see [Amazon S3 Permissions](s3-permissions.md)\. If the dataset or Athena query results are encrypted, you may need additional permissions\. For more information, see [Configuring Encryption Options](encryption.md)\.