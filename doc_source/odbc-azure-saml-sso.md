# Configuring SSO for Azure AD Single\-Account Access using ODBC<a name="odbc-azure-saml-sso"></a>

AWS single\-account access enables you to federate Azure AD to a single AWS account and use Azure AD to manage access to AWS IAM roles\. For each AWS account, Azure AD administrators federate to AWS IAM, assign users or groups to the account, and configure Azure AD to authorize role access\.

**Note**  
The directions provided here are specific to Athena\. For general instructions on configuring Azure Active Directory SSO for use with AWS, see [Tutorial: Azure Active Directory single sign\-on \(SSO\) integration with AWS Single\-Account Access](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) in the Microsoft documentation\.

**Important**  
This tutorial shows you how to configure Azure Active Directory and ODBC for single sign\-on to Athena with and without MFA support\. The MFA and non\-MFA settings are slightly different\. For the **Reply URL** and **SAML:aud** values, non\-MFA support uses defaults, but MFA support uses **http://localhost:/athena**\.

## Step 1: Creating an Azure Enterprise Application<a name="odbc-azure-saml-sso-step-1-creating-an-azure-enterprise-application"></a>

To begin, you create an enterprise application in the Azure AD portal\. When configuration is complete, your application will control your Azure users' access to Athena\.

**To create an AWS single\-account access application in the Azure AD portal**

1. Login to the Azure AD portal at [https://portal\.azure**\.**com](https://portal.azure.com)\.

1. Choose **Azure Active Directory**\.  
![\[Choose Azure Active Directory in the Azure portal.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-1.png)

1. In the navigation pane, choose **Enterprise applications**\.  
![\[Choose Enterprise applications.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-2.png)

1. Choose **New application**\.  
![\[Choose New application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-3.png)

1. In the **Browse Azure AD Gallery** page search box, enter **AWS**, and then choose **AWS Single\-Account Access**\.  
![\[Choose AWS Single-Account Access.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-4.png)

1. In the right pane, for **Name**, enter a name for your application\. The default name is **AWS Single\-Account Access**\.

1. Choose **Create**\.  
![\[Choose Create.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-5.png)

   The creation process can take a minute or two to complete\.

1. On the **AWS Single\-Account Access** page, in left navigation pane, choose **Single sign\-on**\.  
![\[Choose Single sign-on.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-6.png)

1. For **Select a single sign\-on method**, choose **SAML**\.  
![\[Choose SAML.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-7.png)

1. At the **Save single sign\-on setting** prompt, choose **Yes**\.

1. For the **Basic SAML Configuration** section, do one of the following:
   + If your application does not require multifactor authentication, for **Identifier \(Entity ID\)** and **Reply URL**, confirm that the URL **https://signin\.aws\.amazon\.com/saml** is prepopulated, as the following image shows:  
![\[Basic SAML configuration\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-8.png)
   + If your application requires MFA, choose **Edit**, and then, in the **Basic SAML Configuration** pane, perform the following tasks:

     1. For **Identifier \(Entity ID\)**, enter **urn:amazon:webservices**

     1. For **Reply URL**, enter **http://localhost/athena**

     1. At the top of the **Basic SAML Configuration** pane, choose **Save**, and then choose the **X** icon to close the pane\.

     1. At the **Test single sign\-on** prompt, choose **No, I'll test later**\.  
![\[Basic SAML configuration for MFA.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-9.png)

1. Notice that, in the **User Attributes & Claims** section, the necessary claims are already configured for you\.  
![\[Claims already configured.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-10.png)

1. In the **SAML Signing Certificate** section, for **Federation Metadata XML**, choose **Download**, and then save the XML file in a secure location\.  
![\[Downloading the metadata XML.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-11.png)

   Later, you upload this metadata file to the IAM console to enable communication between Athena and Azure\.

### Adding the Delegated User\.Read Permission to your Application<a name="odbc-azure-saml-sso-adding-the-delegated-user-read-permission"></a>

Next, you give delegated read permission to your enterprise application to sign in with SSO and read user profile information\.

**To add delegated read permissions to your enterprise application**

1. At the top of the Azure portal, choose **Default Directory**\.  
![\[Choose Default Directory.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-12.png)

1. In the navigation pane, choose **App registrations**\.

1. Choose the **All applications** tab, and then choose your enterprise application\.

1. In the navigation pane, choose **API permissions\.**

1. In the **Configured permissions** section, choose **Add a permission**\.

1. In the **Request API permissions** pane on the right, choose **Microsoft Graph**\.

1. For **What type of permissions does your application require?**, choose **Delegated permissions**\.

1. In the **Selection permissions** search box, enter **User\.Read **\.

1. Expand the **User** section\.

1. Select **User\.Read**\.

1. Choose **Add permissions**\.  
![\[Adding the user.read API permission.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-13.png)

1. In the **Configured permissions** section, choose **Grant admin consent for Default Directory**, and then choose **Yes** at the confirmation prompt\.  
![\[Choose Grant admin consent for Default Directory.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-13a.png)

   On the **API Permissions** page, the **Configured permissions** section shows that the **User\.Read** permission has been delegated to your application\.  
![\[User.Read permission added.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-14.png)

1. For more information about the **User\.Read** permission, choose the **User\.Read** link in the **Configured permissions** section\.

### For MFA: Specifying the Athena Redirect URI<a name="odbc-azure-saml-sso-for-mfa-specifying-the-athena-redirect-uri"></a>

Perform the steps in this section only if your connection will use multifactor authentication\.

**To specify a redirect URI in Azure AD for multifactor authentication**

1. At the top of the Azure portal, choose **Default Directory**\.

1. In the navigation pane, choose **App registrations**\.

1. Choose the **All applications** tab, and then choose your enterprise application\.

1. In the left navigation pane, choose **Authentication**\.

1. In the **Platform configurations** section, choose **Add a platform**\.  
![\[Choose Add a platform.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-15.png)

1. In the **Configure platforms** pane on the right, in the **Mobile and desktop applications** section, choose **Mobile and desktop applications**\.

1. In the **Configure Desktop \+ devices** pane, for **Custom redirect URIs**, enter **http://localhost/athena**, and then choose **Configure\.**  
![\[Entering the custom redirect URL.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-16.png)

You have now set up the enterprise application in Azure\. You are ready to set up the IAM SAML identity provider and IAM role to use this Azure federation\.

## Step 2: Using the IAM Console to Configure Azure Access to Athena<a name="odbc-azure-saml-sso-step-2-using-the-iam-console-to-configure-azure-access-to-athena"></a>

In this section, you perform the following tasks in AWS in the IAM management console:
+ Create an identity provider for AWS using the metadata that you downloaded from Azure AD\.
+ Create an IAM role\. When you create the role, you attach policies that allow access to Athena and Amazon S3\. You will assign this role to your Azure AD users so that you can provide them with single sign\-on access to Athena\.
+ Enable Azure AD to list AWS IAM Roles by 1\) creating a policy that grants list roles permissions and then 2\) by creating an IAM user for Azure AD\. When you create the IAM user for Azure AD, you attach the list roles policy\.

### Creating an Identity Provider<a name="odbc-azure-saml-sso-creating-an-identity-provider"></a>

In this section, you use the metadata from the enterprise application that you created in Azure to create a SAML identity provider in the AWS IAM console\.

**To create a SAML identity provider in IAM using your Azure metadata**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.  
![\[Choose IAM.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-17.png)

1. In the navigation pane, choose **Identity providers**, and then choose **Add provider**\.  
![\[Choose Identity providers, Add provider\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-18.png)

1. On the **Add an Identity provider** page, for **Configure provider**, enter the following information\.  
![\[Adding an identity provider in the IAM console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-19.png)
   + For **Provider type**, choose **SAML**\.
   + For **Provider name**, enter a name for your provider \(for example, **AthenaAzureADSSO**\)\.
   + For **Metadata document**, use the **Choose file** option to upload the identity provider \(IdP\) metadata XML file that you downloaded from Azure AD\.

1. Choose **Add provider**\.

### Creating an IAM Role for Athena and Amazon S3 Access<a name="odbc-azure-saml-sso-creating-an-iam-role-for-athena-and-amazon-s3-access"></a>

Next, you create an IAM role for Athena and Amazon S3 access\. You will assign this role to your Azure AD users so that you can provide them with single sign\-on access to Athena\.

**To create an IAM role for Athena and Amazon S3 access**

1. In the IAM console navigation pane, choose **Roles**, and then choose **Create role**\.  
![\[Creating a role in the IAM console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-20.png)

1. Choose **SAML 2\.0 federation**\.

1. In the **Choose a SAML 2\.0 provider** section, for **SAML provider**, choose the SAML identity provider that you created \(for example, **AthenaAzureADSSO**\.\)

1. Do one of the following:
   + If your application does not require multifactor authentication, select **Allow programmatic and AWS Management Console access**\.
   + If your application requires multifactor authentication, select **Allow programmatic access only**\.

1. For the `SAML:aud` **Attribute**, do one of the following:
   + If your application does not require multifactor authentication, make sure that **Value** is set to **https://signin\.aws\.amazon\.com/saml** \(the default\)\.
   + If your application requires MFA and you are using ODBC driver 1\.17 or earlier, for **Value**, enter **http://localhost/athena**\.
   + If your application requires MFA and you are using ODBC driver 1\.18 or later, for **Value**, enter **http://localhost:\*/athena**\. The **\*** accounts for the fact that later ODBC drivers add an ephemeral TCP port\.

1. Choose **Next: Permissions**\.  
![\[Choosing your SAML provider and configuring access.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-21.png)

1. In the **Attach permissions policies** section, attach the necessary policies for your application\. This tutorial attaches the `AmazonAthenaFullAccess` and `AmazonS3FullAccess` policies\.  
![\[Attaching policies to the role.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-22.png)

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. For **Role name**, enter a name for your role \(for example, **AthenaAzureADRole**\)\.

1. Confirm the policies that you chose are present, and then choose **Create role**\.  
![\[Choose Create role,\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-23.png)

### Enabling Azure to List Roles<a name="odbc-azure-saml-sso-enabling-azure-to-list-roles"></a>

To secure federated access, the Azure AD service must know the roles that are available in your account\. To enable this functionality, you create a policy that grants list roles permissions and then create an IAM user for the Azure AD service\. When you create the IAM user for the Azure AD service, you attach the list roles policy to the user\.

#### Create a List Roles Policy<a name="odbc-azure-saml-sso-create-a-list-roles-policy"></a>

In this step, you use the IAM console to create a policy that grants list roles permissions\.

**To create a list roles policy**

1. In the navigation pane of the IAM console, choose **Policies**, and then choose **Create policy**\.

1. On the **Create policy** page, choose **JSON**\.

1. Paste the following `ListRoles` policy into the JSON editor\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "iam:ListRoles"
         ],
         "Resource": "*"
       }
     ]
   }
   ```  
![\[Entering the ListRoles policy into the JSON editor in the IAM console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-24.png)

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. On the **Review policy** page, enter a name for the policy\. This walkthrough uses the name `AthenaListRolesAzure`\.  
![\[Naming and creating the ListRoles policy in the IAM console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-25.png)

1. Choose **Create policy**\.

#### Create a User for the Azure AD service and attach the ListRoles policy to it<a name="odbc-azure-saml-sso-create-azure-service-user-and-attach-listroles-policy"></a>

Now you are ready to create an IAM user that Azure can use to list the roles that are available to it\.

**To create a user for the Azure AD service and attach the list roles policy to the user**

1. In the navigation pane of the IAM console, choose **Users**, and then choose **Add users**\.

1. On the **Add user** page, for **User name**, enter a user name\. This tutorial uses the name **AthenaAzureADRoleProvision**\.

1. In the **Select AWS Access type** section, choose **Access key \- Programmatic access**\.

1. Choose **Next: Permissions**\.

1. On the **Set permissions** page, choose **Attach existing policies directly**\.  
![\[Attaching the ListRoles policy to the user.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-26.png)

1. For **Filter policies**, enter the name of the list roles policy that you just created \(for example, **AthenaListRolesAzure**\), and then select the check box for the policy\.

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. Choose **Create user**\.

1. On the **Success** page, copy the **Access key ID** and **Secret access key**\. You will add these to Azure next\.

## Step 3: Configuring Role Provisioning in Azure AD<a name="odbc-azure-saml-sso-step-3-configuring-role-provisioning-in-azure-ad"></a>

Next, in the Azure AD portal, you add AWS access keys to Azure and configure the Azure provisioning service so that it can import roles from AWS automatically\.

**To configure role provisioning in the Azure AD portal**

1. Sign in to the Azure AD portal at [https://portal\.azure**\.**com](https://portal.azure.com)\.

1. In the navigation pane, choose **Enterprise Applications**\.

1. Choose your enterprise application\.

1. In the navigation pane, choose **Provisioning**\.

1. Choose **Get started**\.

1. In the **Provisioning** pane, for **Provisioning Mode**, choose **Automatic**\.

1. Expand the **Admin Credentials** section, and then enter the required information\.  
![\[Entering admin keys to enable role provisioning.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-27.png)
   + For **clientsecret**, enter the access key ID of the IAM user for Azure that you created in the IAM console \(in this walkthrough, the access key ID for **AthenaAzureADRoleProvision**\.\)
   + For **Secret Token**, enter the corresponding secret access key\.

1. Choose **Test Connection** and verify that test completes successfully\.  
![\[Test connection successful.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-28.png)

1. At the top of the **Provisioning** pane, choose **Save**\.

1. Choose the **X** icon to close the **Provisioning** pane where you entered the admin credentials and tested the connection\. You can return to this page later by choosing **Edit provisioning** from the main **Provisioning** page\.

1. On the main **Provisioning** page for your application, choose **Start provisioning**\. The provisioning process can take a few minutes\. To update the status, you can choose **Refresh** at the top of the **Provisioning** page\.

   When the provisioning is done, the Azure AD portal reports the completion under **Current cycle status** and displays the number of roles that it imported\.  
![\[Provisioning complete.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-29.png)

## Step 4: Creating and Configuring Users in Azure AD<a name="odbc-azure-saml-sso-step-4-creating-and-configuring-users-in-azure-ad"></a>

At this point, you have created your AWS SSO application\. And, thanks to the provisioning that you performed, Azure AD is aware of the IAM roles available to it\. This means that when you create users in Azure AD, you can now assign them to your AWS SSO application and to the IAM role that provides access to Athena and Amazon S3\.

In this section, you:
+ Create the test user **Athena Test User** in Azure AD\.
+ Assign **Athena Test User** to your **AWS Single\-Account Access** application\.
+ Assign **Athena Test User** to the **AthenaAzureADRole** so that it has access to Athena\.

### Creating a Test User in Azure AD<a name="odbc-azure-saml-sso-creating-a-test-user-in-azure-ad"></a>

The following procedure creates an Athena test user called **Athena Test User** in the Azure AD portal\.

**To create a test user in Azure AD**

1. At the top of the Azure portal, choose **Default Directory**\.

1. In the **Default Directory** navigation pane, choose **Users**\.  
![\[Choose Users.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-30.png)

1. Choose **New user** to create new Azure AD test user\.  
![\[Choose New user.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-31.png)

1. Choose **Create user** or **Invite user**\.

1. Enter the information for the new user, and then choose **Create** or **Invite**\. For this tutorial, a user called **Athena Test User** was created using the **Invite** option\.

### Assigning Athena Users to Your Azure AD Application and IAM Role<a name="odbc-azure-saml-sso-assigning-athena-users-to-your-azure-ad-application-and-iam-role"></a>

Now that you have an Athena test user, you can assign it to your SSO application and to the AthenaAzureADRole that allows the user to access Athena and Amazon S3\.

**To assign an Azure AD user to your application and IAM role**

1. In the Azure AD portal, choose **Default Directory**, **Enterprise applications**, and then choose your application\.

1. In the navigation pane, choose **Users and Groups**, and then choose **Add user/group**\.  
![\[Choose Add user/group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-32.png)

1. On the **Add Assignment** page, for **Users**, choose **None Selected**\.  
![\[Choose None Selected to select a user to assign to the application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-33.png)

1. In the **Users** pane on the right, find and select your Athena test user, and then choose **Select**\.  
![\[Selecting a Azure user for your enterprise application in Azure AD.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-34.png)

1. On the **Add Assignment** page, for **Select a role**, choose **None Selected**\.

1. In the **Select a role** pane on the right, find and select your AthenaAzureADRole\. Both the role and the identity provider name are displayed\.  
![\[Selecting a role in the Azure AD portal.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-35.png)

1. Choose **Select**\.

1. On the **Add Assignment** page, choose **Assign**\.  
![\[Assigning a user and role to the Azure AD enterprise application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-36.png)

   The **Athena Test User** appears in the list of users for the **AWS Single\-Account Access** application with the **AthenaAzureADRole** assigned\.  
![\[Azure user has been assigned to the enterprise application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-37.png)

## Step 5: Configuring ODBC<a name="odbc-azure-saml-sso-step-5-configuring-odbc"></a>

To configure your ODBC connection, you obtain your client and tenant IDs from the Azure portal, and then enter these values using the ODBC Data Sources program in Microsoft Windows\.

### Azure AD Portal: Obtaining Your Client and Tenant ID<a name="odbc-azure-saml-sso-azure-ad-portal-obtaining-your-client-and-tenant-id"></a>

You can obtain your client and tenant IDs from the **App registrations** section of the Azure portal\. The following procedure shows how\.

**To get your client and tenant ID from the Azure AD portal**

1. On the Azure AD portal **Default Directory** page, in the left navigation pane, choose **App registrations\.**

1. Choose the **All applications** tab, and then choose your enterprise application\.

1. In the **Essentials** section, find your UUIDs for **Application \(client\) ID** and **Directory \(tenant\) ID**\.  
![\[Copying the client and tenant IDs from the Azure application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-38.png)

1. Use the **Copy to clipboard** icon to copy and store these values\. You will use these values in the next section to configure the Athena ODBC connection to Azure AD\.

### Windows: Configuring Athena ODBC for Azure AD<a name="odbc-azure-saml-sso-windows-configuring-athena-odbc-for-azure-ad"></a>

This section shows you how to use the ODBC Data Sources program in Windows to configure your Athena ODBC connection for Azure AD\.

**To configure a browser Azure AD ODBC connection to Athena**

1. Install the ODBC driver for Athena\. For download links, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

1. In Windows, launch the **ODBC Data Sources** program\.

1. In the **ODBC Data Source Administrator** program, choose **Add**\.  
![\[Adding a data source to the ODBC Data Sources program in Microsoft Windows.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-39.png)

1. Choose **Simba Athena ODBC Driver**, and then choose **Finish**\.  
![\[Choose Simba Athena ODBC Driver.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-40.png)

1. In the **Simba Athena ODBC Driver DSN Setup** dialog, enter the values described\.  
![\[\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-41.png)
   + For **Data Source Name,** enter a name for your data source \(for example, **Athena ODBC 64**\)\.
   + For **Description**, enter a description for your data source\.
   + For **AWS Region**, enter the AWS Region that you are using \(for example, **us\-west\-1**\)\.
   + For **S3 Output Location**, enter the Amazon S3 path where you want your output to be stored\.

1. Choose **Authentication Options**\.

1. In the **Authentication Options** dialog box, choose or enter the values described\.  
![\[Entering authentication options for the ODBC connection.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-azure-saml-sso-42.png)
   + For **Authentication Type**, choose one of the following:
     + If you are using MFA, choose **BrowserAzureAD**\.
     + If you are not using MFA, choose either **BrowserAzureAD** or **AzureAD**\.
   + For **User**, enter the Azure AD user name\.
   + For **Password**, enter the Azure AD password\.
   + For **Tenant ID**, enter the value for the **Directory \(tenant\) ID** that you copied from the **Overview** page of your enterprise application in the Azure AD portal\.
   + For **Client ID**, enter the value for the **Application \(client\) ID** that you copied from the **Overview** page of your enterprise application in the Azure AD portal\.

1. Choose **OK**\.

1. Choose **Test** to test the connection, or **OK** to finish\.

### ODBC\.INI File Configuration Example<a name="odbc-azure-saml-sso-odbc-ini-file-configuration-example"></a>

After you finish, the `ODBC.INI` file in your Windows directory \(for example, `C:\\Windows\ODBC.INI`\) should have a DSN configuration entry like the following:

```
[Athena BrowserAzureAD Test]
Description=Simba Athena ODBC Driver (64-bit) DSN
Driver=/opt/simba/athenaodbc/lib/64/libathenaodbc_sb64.so
AwsRegion=[YOUR-REGION]
Workgroup=[YOUR-WORKGROUP]
S3OutputLocation=[YOUR-OUTPUTLOCATION]
AuthenticationType=BrowserAzureAD
UID=[AzureAD USERNAME]
PWD=[AzureAD PASSWORD]
idp_tenant=[YOUR TENANT ID]
client_id=[YOUR CLIENT ID]
```