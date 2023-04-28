# Configuring SSO for ODBC using the Okta plugin and Okta Identity Provider<a name="odbc-okta-plugin"></a>

This page describes how to configure the Amazon Athena ODBC driver and Okta plugin to add single sign\-on \(SSO\) capability using the Okta identity provider\.

## Prerequisites<a name="odbc-okta-plugin-prerequisites"></a>

Completing the steps in this tutorial requires the following:
+ Amazon Athena ODBC driver\. For download links, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.
+ An IAM Role that you want to use with SAML\. For more information, see [Creating a role for SAML 2\.0 federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp_saml.html) in the *IAM User Guide*\.
+ An Okta account\. For information, visit [Okta\.com](https://www.okta.com/)\.

## Creating an app integration in Okta<a name="odbc-okta-plugin-creating-an-app-integration-in-okta"></a>

First, use the Okta dashboard to create and configure a SAML 2\.0 app for single sign\-on to Athena\. You can use an existing Redshift application in Okta to configure access to Athena\.

**To create an app integration in Okta**

1. Log in to the admin page for your account on [Okta\.com](https://www.okta.com/)\.

1. In the navigation panel, choose **Applications**, **Applications\.**

1. On the **Applications** page, choose **Browse App Catalog\.**

1. On the **Browse App Integration Catalog** page, in the **Use Case** section, choose **All Integrations**\.

1. In the search box, enter **Amazon Web Services Redshift**, and then choose **Amazon Web Services Redshift SAML**\.

1. Choose **Add Integration**\.  
![\[Choose Add integration.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-1.png)

1. In the **General Settings Required** section, for **Application label**, enter a name for the application\. This tutorial uses the name **Athena\-ODBC\-Okta\.**  
![\[Enter a name for the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-2.png)

1. Choose **Done**\.

1. On the page for your Okta application \(for example, **Athena\-ODBC\-Okta**\), choose **Sign On**\.  
![\[Choose the Sign On tab.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-3.png)

1. In the **Settings** section, choose **Edit**\.  
![\[Choose Edit.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-4.png)

1. In the **Advanced Sign\-on Settings** section, configure the following values\.
   + For **IdP ARN and Role ARN**, enter your AWS IDP ARN and Role ARN as comma\-separated values\. For information about the IAM role format, see [Configuring SAML assertions for the authentication response](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml_assertions.html) in the *IAM User Guide*\.
   + For **Session Duration**, enter a value between 900 and 43200 seconds\. This tutorial uses the default of 3600 \(1 hour\)\.  
![\[Enter advanced sign-on settings.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-5.png)

   The **DbUser Format**, **AutoCreate**, and **Allowed DBGroups** settings aren't used by Athena\. You don't have to configure them\.

1. Choose **Save**\.

## Retrieve ODBC configuration information from Okta<a name="odbc-okta-plugin-retrieve-odbc-configuration-information-from-okta"></a>

Now that you created the Okta application, you're ready to retrieve the application's ID and IdP host URL\. You will require these later when you configure ODBC for connection to Athena\.

**To retrieve configuration information for ODBC from Okta**

1. Choose the **General** tab of your Okta application, and then scroll down to the **App Embed Link** section\.  
![\[The embed link URL of the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-6.png)

   Your **Embed Link** URL is in the following format:

   ```
   https://trial-1234567.okta.com/home/amazon_aws_redshift/Abc1de2fghi3J45kL678/abc1defghij2klmNo3p4
   ```

1. From your **Embed Link** URL, extract and save the following pieces:
   + The first segment after `https://`, up to and including `okta.com` \(for example, **trial\-1234567\.okta\.com**\)\. This is your IdP host\.
   + The last two segments of the URL, including the forward slash in the middle\. The segments are two 20\-character strings with a mix of numbers and upper and lowercase letters \(for example, **Abc1de2fghi3J45kL678/abc1defghij2klmNo3p4**\)\. This is your application ID\.

## Add a user to the Okta application<a name="odbc-okta-plugin-add-a-user-to-the-okta-application"></a>

Now you're ready to add a user to your Okta application\.

**To add a user to the Okta application**

1. In the left navigation pane, choose **Directory**, and then choose **People**\.

1. Choose **Add person**\.  
![\[Choose Add person.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-7.png)

1. In the **Add Person** dialog box, enter the following information\.
   + Enter values for **First name** and **Last name**\. This tutorial uses **test user**\.
   + Enter values for **Username** and **Primary email**\. This tutorial uses **test@amazon\.com** for both\. Your security requirements for passwords might vary\.  
![\[Enter user credentials.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-8.png)

1. Choose **Save**\.

Now you're ready to assign the user that you created to your application\.

**To assign the user to your application:**

1. In the navigation pane, choose **Applications**, **Applications**, and then choose the name of your application \(for example, **Athena\-ODBC\-Okta**\)\.

1. Choose **Assign,** and then choose **Assign to People**\.  
![\[Choose Assign to People.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-9.png)

1. Choose the **Assign** option for your user, and then choose **Done**\.  
![\[Choose Assign, and then choose Done.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-10.png)

1. At the prompt, choose **Save and Go Back**\. The dialog box shows the user's status as **Assigned**\.

1. Choose **Done**\.

1. Choose the **Sign On** tab\.

1. Scroll down to the **SAML Signing Certificates** section\.

1. Choose **Actions**\.

1. Open the context \(right\-click\) menu for **View IdP metadata**, and then choose the browser option to save the file\.

1. Save the file with an `.xml` extension\.  
![\[Saving IdP metadata to a local XML file.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-11.png)

## Create an AWS SAML Identity Provider and Role<a name="odbc-okta-plugin-create-an-aws-saml-identity-provider-and-role"></a>

Now you are ready to upload the metadata XML file to the IAM console in AWS\. You will use this file to create an AWS SAML identity provider and role\. Use an AWS Services administrator account to perform these steps\.

**To create a SAML identity provider and role in AWS**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/IAM/](https://console.aws.amazon.com/IAM/)\.

1. In the navigation pane, choose **Identity providers**, and then choose **Add provider**\.  
![\[Choose Add provider.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-12.png)

1. On the **Add an Identity provider** page, for **Configure provider**, enter the following information\.
   + For **Provider type**, choose **SAML**\.
   + For **Provider name**, enter a name for your provider \(for example, ** AthenaODBCOkta**\)\.
   + For **Metadata document**, use the **Choose file** option to upload the identity provider \(IdP\) metadata XML file that you downloaded\.  
![\[Enter information for the identity provider.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-13.png)

1. Choose **Add provider**\.

### Creating an IAM role for Athena and Amazon S3 access<a name="odbc-okta-plugin-creating-an-iam-role-for-athena-and-amazon-s3-access"></a>

Now you are ready to create an IAM role for Athena and Amazon S3 access\. You will assign this role to your user\. That way, you can provide the user with single sign\-on access to Athena\.

**To create an IAM role for your user**

1. In the IAM console navigation pane, choose **Roles**, and then choose **Create role**\.  
![\[Choose Create role.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-14.png)

1. On the **Create role** page, choose the following options:
   + For **Select type of trusted entity**, choose **SAML 2\.0 Federation\.**
   + For **SAML 2\.0–based provider**, choose the SAML identity provider that you created \(for example, **AthenaODBCOkta**\)\.
   + Select **Allow programmatic and AWS Management Console access**\.  
![\[Choose options on the Create role page.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-15.png)

1. Choose **Next**\.

1. On the **Add Permissions** page, for **Filter policies**, enter **AthenaFull**, and then press ENTER\.

1. Select the `AmazonAthenaFullAccess` managed policy, and then choose **Next**\.  
![\[Choose the AmazonAthenaFullAccess managed policy.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-16.png)

1. On the **Name, review, and create** page, for **Role name**, enter a name for the role \(for example, **Athena\-ODBC\-OktaRole**\), and then choose **Create role**\.

## Configuring the Okta ODBC connection to Athena<a name="odbc-okta-plugin-configuring-the-okta-odbc-connection-to-athena"></a>

Now you're ready to configure the Okta ODBC connection to Athena using the ODBC Data Sources program in Windows\.

**To configure your Okta ODBC connection to Athena**

1. In Windows, launch the **ODBC Data Sources** program\.

1. In the **ODBC Data Source Administrator** program, choose **Add**\.  
![\[Choose Add.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-17.png)

1. Choose **Simba Athena ODBC Driver**, and then choose **Finish**\.  
![\[Choose the Athena ODBC driver.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-18.png)

1. In the **Simba Athena ODBC Driver DSN Setup** dialog, enter the values described\.
   + For **Data Source Name,** enter a name for your data source \(for example, **Athena ODBC 64**\)\.
   + For **Description**, enter a description for your data source\.
   + For **AWS Region**, enter the AWS Region that you're using \(for example, **us\-west\-1**\)\.
   + For **S3 Output Location**, enter the Amazon S3 path where you want your output to be stored\.  
![\[Enter values for the data source name setup.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-19.png)

1. Choose **Authentication Options**\.

1. In the **Authentication Options** dialog box, choose or enter the following values\.
   + For **Authentication Type**, choose **Okta**\.
   + For **User**, enter your Okta user name\.
   + For **Password**, enter your Okta password\.
   + For **IdP Host**, enter the value that you recorded earlier \(for example, **trial\-1234567\.okta\.com**\)\.
   + For **IdP Port**, enter **443**\.
   + For **App ID**, enter the value that you recorded earlier \(the last two segments of your Okta embed link\)\.
   + For **Okta App Name**, enter **amazon\_aws\_redshift**\.  
![\[Enter the authentication options.\]](http://docs.aws.amazon.com/athena/latest/ug/images/odbc-okta-plugin-20.png)

1. Choose **OK**\.

1. Choose **Test** to test the connection or **OK** to finish\.