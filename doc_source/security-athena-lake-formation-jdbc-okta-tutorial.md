# Tutorial: Configuring Federated Access for Okta Users to Athena Using Lake Formation and JDBC<a name="security-athena-lake-formation-jdbc-okta-tutorial"></a>

This tutorial shows you how to configure Okta, AWS Lake Formation, AWS Identity and Access Management permissions, and the Athena JDBC driver to enable SAML\-based federated use of Athena\. Lake Formation provides fine\-grained access control over the data that is available in Athena to the SAML\-based user\. To set up this configuration, the tutorial uses the Okta developer console, the AWS IAM and Lake Formation consoles, and the SQL Workbench/J tool\.
<a name="security-athena-lake-formation-jdbc-okta-tutorial-prerequisites"></a>
**Prerequisites**  
This tutorial assumes that you have done the following:
+ Created an AWS account\. To create an account, visit the [Amazon Web Services home page](https://aws.amazon.com/)\.
+ [Set up a query results location](querying.md#query-results-specify-location) for Athena in Amazon S3\.
+ [Registered an Amazon S3 data bucket location](https://docs.aws.amazon.com/lake-formation/latest/dg/register-data-lake.html) with Lake Formation\.
+ Defined a [database](https://docs.aws.amazon.com/glue/latest/dg/define-database.html) and [tables](https://docs.aws.amazon.com/glue/latest/dg/tables-described.html) on the [AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) that point to your data in Amazon S3\.
  + If you have not yet defined a table, either [run a AWS Glue crawler](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html) or [use Athena to define a database and one or more tables](work-with-data.md) for the data that you want to access\.
  + This tutorial uses a table based on the [NYC Taxi trips dataset](https://registry.opendata.aws/nyc-tlc-trip-records-pds/) available in the [Registry of Open Data on AWS](https://registry.opendata.aws/)\. The tutorial uses the database name `tripdb` and the table name `nyctaxi`\.

**Topics**
+ [Step 1: Create an Okta Account](#security-athena-lake-formation-jdbc-okta-tutorial-step-1-create-an-okta-account)
+ [Step 2: Add users and groups to Okta](#security-athena-lake-formation-jdbc-okta-tutorial-step-2-set-up-an-okta-application-for-saml-authentication)
+ [Step 3: Set up an Okta Application for SAML Authentication](#security-athena-lake-formation-jdbc-okta-tutorial-step-3-set-up-an-okta-application-for-saml-authentication)
+ [Step 4: Create an AWS SAML Identity Provider and Lake Formation Access IAM Role](#security-athena-lake-formation-jdbc-okta-tutorial-step-4-create-an-aws-saml-identity-provider-and-lake-formation-access-IAM-role)
+ [Step 5: Add the IAM Role and SAML Identity Provider to the Okta Application](#security-athena-lake-formation-jdbc-okta-tutorial-step-5-update-the-okta-application-with-the-aws-role-and-saml-identity-provider)
+ [Step 6: Grant user and group permissions through AWS Lake Formation](#security-athena-lake-formation-jdbc-okta-tutorial-step-6-grant-permissions-through-aws-lake-formation)
+ [Step 7: Verify access through the Athena JDBC client](#security-athena-lake-formation-jdbc-okta-tutorial-step-7-verify-access-through-athena-jdbc-client)
+ [Conclusion](#security-athena-lake-formation-jdbc-okta-tutorial-conclusion)
+ [Related Resources](#security-athena-lake-formation-jdbc-okta-tutorial-related-resources)

## Step 1: Create an Okta Account<a name="security-athena-lake-formation-jdbc-okta-tutorial-step-1-create-an-okta-account"></a>

This tutorial uses Okta as a SAML\-based identity provider\. If you do not already have an Okta account, you can create a free one\. An Okta account is required so that you can create an Okta application for SAML authentication\.

**To create an Okta account**

1. To use Okta, navigate to the [Okta developer sign up page](https://developer.okta.com/signup/) and create a free Okta trial account\. The Developer Edition Service is free of charge up to the limits specified by Okta at [developer\.okta\.com/pricing](developer.okta.com/pricing)\.

1. When you receive the activation email, activate your account\. 

   An Okta domain name will be assigned to you\. Save the domain name for reference\. Later, you use the domain name \(*<okta\-idp\-domain>*\) in the JDBC string that connects to Athena\.

## Step 2: Add users and groups to Okta<a name="security-athena-lake-formation-jdbc-okta-tutorial-step-2-set-up-an-okta-application-for-saml-authentication"></a>

In this step, you use the Okta console to perform the following tasks:
+ Create two Okta users\.
+ Create two Okta groups\.
+ Add one Okta user to each Okta group\.

**To add users to Okta**

1. After you activate your Okta account, log in as administrative user to the assigned Okta domain\.

1. If you are in the **Developer Console**, use the option on the top left of the page to choose the **Classic UI**\.  
![\[Choose the classic UI.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-1.png)

1. In the **Classic UI**, choose **Directory**, and then choose **People**\.

1. Choose **Add Person** to add a new user who will access Athena through the JDBC driver\.  
![\[Choose Add Person.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-3.png)

1. In the **Add Person** dialog box, enter the required information\.
   + Enter values for **First name** and **Last name**\. This tutorial uses *athena\-okta\-user*\.
   + Enter a **Username** and **Primary email**\. This tutorial uses *athena\-okta\-user@anycompany\.com*\.
   + For **Password**, choose **Set by admin**, and then provide a password\. This tutorial clears the option for **User must change password on first login**; your security requirements may vary\.  
![\[Adding a user to the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4.png)

1. Choose **Save and Add Another**\.

1. Enter the information for another user\. This example adds the business analyst user *athena\-ba\-user@anycompany\.com*\.  
![\[Adding a user to the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4a.png)

1. Choose **Save**\.

In the following procedure, you provide access for two Okta groups through the Athena JDBC driver by adding a "Business Analysts" group and a "Developer" group\.

**To add Okta groups**

1. From the Okta classic UI, choose **Directory**, and then choose **Groups**\.

1. On the **Groups** page, choose **Add Group**\.  
![\[Choose Add Group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4c.png)

1. In the **Add Group** dialog box, enter the required information\.
   + For **Name**, enter *lf\-business\-analyst*\.
   + For **Group Description**, enter *Business Analysts*\.  
![\[Adding an Okta group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4d.png)

1. Choose **Add Group**\.

1. On the **Groups** page, choose **Add Group** again\. This time you will enter information for the Developer group\.

1. Enter the required information\.
   + For **Name**, enter *lf\-developer*\.
   + For **Group Description**, enter *Developers*\.  
![\[Adding a "Developers" Okta group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4e.png)

1. Choose **Add Group**\.

Now that you have two users and two groups, you are ready to add a user to each group\.

**To add users to groups**

1. On the **Groups** page, choose the **lf\-developer** group that you just created\. You will add one of the Okta users that you created as a developer to this group\.  
![\[Choose lf-developer.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4f.png)

1. Choose **Manage People**\.  
![\[Choose Manage People.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4g.png)

1. From the **Not Members** list, choose **athena\-okta\-user**\.   
![\[Choose a user to add to the members list.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4h.png)

   The entry for the user moves from the **Not Members **list on the left to the **Members **list on the right\.   
![\[Okta user added to an Okta group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4i.png)

1. Choose **Save**\.

1. Choose **Back to Groups**, or choose **Directory**, and then choose **Groups**\.

1. Choose the **lf\-business\-analyst** group\.

1. Choose **Manage People**\.

1. Add the **athena\-ba\-user** to the **Members** list of the **lf\-business\-analyst** group, and then choose **Save**\. 

1. Choose **Back to Groups**, or choose **Directory**, **Groups**\.

   The **Groups** page now shows that each group has one Okta user\.  
![\[One user has been added to each Okta group in the Okta console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-4j.png)

## Step 3: Set up an Okta Application for SAML Authentication<a name="security-athena-lake-formation-jdbc-okta-tutorial-step-3-set-up-an-okta-application-for-saml-authentication"></a>

In this step, you use the Okta developer console to perform the following tasks:
+ Add a SAML application for use with AWS\.
+ Assign the application to the Okta user\.
+ Assign the application to an Okta group\.
+ Download the resulting identity provider metadata for later use with AWS\.

**To add an application for SAML authentication**

1. From the menu, choose **Applications** so that you can configure an Okta application for SAML authentication to Athena\.

1. Click **Add Application**\.  
![\[Click Add Application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-6.png)

1. In the search box, search for **Redshift**\.

1. Choose **Amazon Web Services Redshift**\. The Okta application in this tutorial uses the existing SAML integration for Amazon Redshift\.  
![\[Choose Amazon Web Services Redshift.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-7.png)

1. On the **Amazon Web Services Redshift** page, choose **Add** to create a SAML\-based application for Amazon Redshift\.  
![\[Choose Add to create a SAML-based application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-8.png)

1. For **Application label**, enter `Athena-LakeFormation-Okta`, and then choose **Done**\.  
![\[Enter a name for the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-9.png)

Now that you have created an Okta application, you can assign it to the users and groups that you created\.

**To assign the application to users and groups**

1. On the application **Assignments** tab, choose **Assign**, **Assign to People**\.  
![\[Choose Assign, Assign to People.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-10.png)

1. In the **Assign Athena\-LakeFormation\-Okta to People** dialog box, find the **athena\-okta\-user** user that you created previously\.

1. Choose **Assign** to assign the user to the application\.  
![\[Choose Assign.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-11.png)

1. Choose **Save and Go Back**\.  
![\[Choose Save and Go Back.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-12.png)

1. Choose **Done**\.

1. On the **Assignments** tab for the **Athena\-LakeFormation\-Okta** application, choose **Assign**, **Assign to Groups**\.   
![\[Choose Assign to Groups.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-12a.png)

1. For **lf\-business\-analyst**, choose **Assign** to assign the **Athena\-LakeFormation\-Okta** application to the **lf\-business\-analyst** group, and then choose **Done**\.  
![\[Assigning an Okta application to an Okta user group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-12b.png)

   The group appears in the list of groups for the application\.  
![\[The Okta application is assigned to the Okta group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-12c.png)

Now you are ready to download the identity provider application metadata for use with AWS\.

**To download the application metadata**

1. Choose the Okta application **Sign On** tab, and then right\-click **Identity Provider metadata**\.  
![\[Right-click Identity Provider metadata.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-13.png)

1. Choose **Save Link As** to save the identity provider metadata, which is in XML format, to a file\. Give it a name that you recognize \(for example, `Athena-LakeFormation-idp-metadata.xml`\)\.  
![\[Saving the identity provider metadata.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-14.png)

## Step 4: Create an AWS SAML Identity Provider and Lake Formation Access IAM Role<a name="security-athena-lake-formation-jdbc-okta-tutorial-step-4-create-an-aws-saml-identity-provider-and-lake-formation-access-IAM-role"></a>

In this step, you use the AWS Identity and Access Management \(IAM\) console to perform the following tasks:
+ Create an identity provider for AWS\.
+ Create an IAM role for Lake Formation access\.
+ Add the AmazonAthenaFullAccess managed policy to the role\.
+ Add a policy for Lake Formation and AWS Glue to the role\.
+ Add a policy for Athena query results to the role\.

**To create an AWS SAML identity provider**

1. Sign in to the **AWS account** **console** as **AWS account administrator** and navigate to the **IAM** console \([https://console\.aws\.amazon\.com/IAM](https://console.aws.amazon.com/IAM)\)

1. In the navigation pane, choose **Identity providers**, and then click **Create Provider**\.  
![\[Creating an identity provider in the IAM console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-15.png)

1. On the **Configure Provider** screen, enter the following information:
   + For Provider Type, choose SAML\.
   + For **Provider Name**, enter `AthenaLakeFormationOkta`\.
   + For **Metadata Document**, choose the identity provider \(IdP\) metadata XML file that you downloaded\.  
![\[Configuring a SAML identity provider.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-16.png)

1. Choose **Next Step**\.

1. On the **Verify Provider Information** page, choose **Create**\.  
![\[Choose Create.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-17.png)

   In the IAM console, the **AthenaLakeFormationOkta** provider that you created appears in the list of identity providers\.  
![\[Identity provider created in the IAM console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-18.png)

Next, you create an IAM role for AWS Lake Formation access\. You add two inline policies to the role\. One policy provides permissions to access Lake Formation and the AWS Glue APIs\. The other policy provides access to Athena and the Athena query results location in Amazon S3\.

**To create an IAM role for AWS Lake Formation access**

1. In the IAM console navigation pane, choose **Roles**, and then choose **Create role**\.  
![\[Creating an IAM role.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-19.png)

1. On the **Create role** page, perform the following steps:

   1. For **Select type of trusted entity**, choose **SAML 2\.0 Federation\.**

   1. For **SAML provider**, select **AthenaLakeFormationOkta**\.

   1. For **SAML provider**, select the option **Allow programmatic and AWS Management Console access**\.

   1. Choose **Next: Permissions**\.  
![\[Configuring an IAM role to use SAML 2.0.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-20.png)

1. On the **Attach Permissions policies** page, for **Filter policies**, enter **Athena**\.

1. Select the **AmazonAthenaFullAccess** managed policy, and then choose **Next: Tags**\.  
![\[Attaching the AmazonAthenaFullAccess managed policy to the IAM role.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-21.png)

1. On the **Add tags** page, choose **Next: Review**\.

1. On the **Review** page, for **Role name**, enter a name for the role \(for example, *Athena\-LakeFormation\-OktaRole*\), and then choose **Create role**\.  
![\[Enter a name for the IAM role.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-22.png)

Next, you add inline policies that allow access to Lake Formation, AWS Glue APIs, and Athena query results in Amazon S3\.

**To add an inline policy to the role for Lake Formation and AWS Glue**

1. From the list of roles in the IAM console, choose the newly created `Athena-LakeFormation-OktaRole`\.

1. On the **Summary** page for the role, on the **Permissions** tab, choose **Add inline policy**\.

1. On the **Create policy** page, choose **JSON**\.

1. Add an inline policy like the following that provides access to Lake Formation and the AWS Glue APIs\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": {
           "Effect": "Allow",
           "Action": [
               "lakeformation:GetDataAccess",
               "lakeformation:GetMetadataAccess",
               "glue:GetUnfiltered*",
               "glue:GetTable",
               "glue:GetTables",
               "glue:GetDatabase",
               "glue:GetDatabases",
               "glue:CreateDatabase",
               "glue:GetUserDefinedFunction",
               "glue:GetUserDefinedFunctions"
           ],
           "Resource": "*"
       }
   }
   ```

1. Choose **Review policy**\.

1. For **Name**, enter a name for the policy \(for example, **LakeFormationGlueInlinePolicy**\)\.

1. Choose **Create policy**\.

**To add an inline policy to the role for the Athena query results location**

1. On the **Summary** page for the `Athena-LakeFormation-OktaRole` role, on the **Permissions** tab, choose **Add inline policy**\.

1. On the **Create policy** page, choose **JSON**\.

1. Add an inline policy like the following that allows the role access to the Athena query results location\. Replace the *<athena\-query\-results\-bucket>* placeholders in the example with the name of your Amazon S3 bucket\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "AthenaQueryResultsPermissionsForS3",
               "Effect": "Allow",
               "Action": [
                   "s3:ListBucket",
                   "s3:PutObject",
                   "s3:GetObject"
               ],
               "Resource": [
                   "arn:aws:s3:::<athena-query-results-bucket>",
                   "arn:aws:s3:::<athena-query-results-bucket>/*"
               ]
           }
       ]
   }
   ```

1. Choose **Review policy**\.

1. For **Name**, enter a name for the policy \(for example, **AthenaQueryResultsInlinePolicy**\)\.

1. Choose **Create policy**\.

Next, you copy the ARN of the Lake Formation access role and the ARN of the SAML provider that you created\. These are required when you configure the Okta SAML application in the next section of the tutorial\.

**To copy the role ARN and SAML identity provider ARN**

1. In the IAM console, on the **Summary** page for the `Athena-LakeFormation-OktaRole` role, choose the **Copy to clipboard** icon next to **Role ARN**\. The ARN has the following format:

   ```
   arn:aws:iam::<account-id>:role/Athena-LakeFormation-OktaRole
   ```

1. Save the full ARN securely for later reference\.

1. In the IAM console navigation pane, choose **Identity providers**\.

1. Choose the **AthenaLakeFormationOkta** provider\.

1. On the **Summary** page, choose the **Copy to clipboard** icon next to **Provider ARN**\. The ARN should look like the following:

   ```
   arn:aws:iam::<account-id>:saml-provider/AthenaLakeFormationOkta
   ```

1. Save the full ARN securely for later reference\.

## Step 5: Add the IAM Role and SAML Identity Provider to the Okta Application<a name="security-athena-lake-formation-jdbc-okta-tutorial-step-5-update-the-okta-application-with-the-aws-role-and-saml-identity-provider"></a>

In this step, you return to the Okta developer console and perform the following tasks:
+ Add user and group Lake Formation URL attributes to the Okta application\.
+ Add the ARN for the identity provider and the ARN for the IAM role to the Okta application\.
+ Copy the Okta application ID\. The Okta application ID is required in the JDBC profile that connects to Athena\.

**To add user and group Lake Formation URL attributes to the Okta application**

1. Sign into the Okta developer console\.

1. Choose the **Applications** tab, and then choose the `Athena-LakeFormation-Okta` application\.  
![\[Choose the Athena-LakeFormation-Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-23.png)

1. Choose on the **Sign On** tab for the application, and then choose **Edit**\.  
![\[Edit the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-24.png)

1. Expand **Attributes \(optional\)**\.

1. Under **Attribute Statements \(optional\)**, add the following attribute:
   + For **Name**, enter **https://lakeformation\.amazon\.com/SAML/Attributes/Username**\.
   + For **Value**, enter **user\.login**  
![\[Adding a user Lake Formation URL attribute to the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-25.png)

1. Under **Group Attribute Statements \(optional\)**, add the following attribute:
   + For **Name**, enter **https://lakeformation\.amazon\.com/SAML/Attributes/Groups**\.
   + For **Name format**, enter **Basic**
   + For **Filter**, choose **Matches regex**, and then enter **\.\*** in the filter box\.  
![\[Adding a group Lake Formation URL attribute to the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-25a.png)

1. Scroll down to the **Advanced Sign\-On Settings** section, where you will add the identity provider and IAM Role ARNs to the Okta application\.

**To add the ARNs for the identity provider and IAM role to the Okta application**

1. For **Idp ARN and Role ARN**, enter the AWS identity provider ARN and role ARN as comma separated values in the format *<saml\-arn>*,*<role\-arn>*\. The combined string should look like the following:

   ```
   arn:aws:iam::<account-id>:saml-provider/AthenaLakeFormationOkta,arn:aws:iam::<account-id>:role/Athena-LakeFormation-OktaRole
   ```  
![\[Entering the identity provider ARN and IAM role ARN in the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-26.png)

1. Choose **Save**\.

Next, you copy the Okta application ID\. You will require this later for the JDBC string that connects to Athena\.

**To find and copy the Okta application ID**

1. Choose the **General** tab of the Okta application\.  
![\[Choose the General tab of the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-27.png)

1. Scroll down to the **App Embed Link** section\.

1. From **Embed Link**, copy and securely save the Okta application ID portion of the URL\. The Okta application ID is the part of the URL after `amazon_aws_redshift/`\.  
![\[Copy the ID of the Okta application.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-28.png)

## Step 6: Grant user and group permissions through AWS Lake Formation<a name="security-athena-lake-formation-jdbc-okta-tutorial-step-6-grant-permissions-through-aws-lake-formation"></a>

In this step, you use the Lake Formation console to grant permissions on a table to the SAML user and group\. You perform the following tasks:
+ Specify the ARN of the Okta SAML user and associated user permissions on the table\.
+ Specify the ARN of the Okta SAML group and associated group permissions on the table\.
+ Verify the permissions that you granted\.

**To grant permissions in Lake Formation for the Okta user**

1. Sign in as data lake administrator to the AWS Management Console\. 

1. Open the Lake Formation console at [https://console\.aws\.amazon\.com/lakeformation/](https://console.aws.amazon.com/lakeformation/)\.

1. From the navigation pane, choose **Tables**, and then select the table that you want to grant permissions for\. This tutorial uses the `nyctaxi` table from the `tripdb` database\.  
![\[Choose the table that you want to grant permissions for.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-29.png)

1. From **Actions**, choose **Grant**\.  
![\[Choose Grant.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-30.png)

1. In the **Grant permissions** dialog, enter the following information:

   1. Under **SAML and Amazon QuickSight users and groups**, enter the Okta SAML user ARN in the following format:

      ```
      arn:aws:iam::<account-id>:saml-provider/AthenaLakeFormationOkta:user/<athena-okta-user>@<anycompany.com>       
      ```

   1. For **Columns**, for **Choose filter type**, and optionally choose **Include columns** or **Exclude columns**\.

   1. Use the **Choose one or more columns** dropdown under the filter to specify the columns that you want to include or exclude for or from the user\.

   1. For **Table permissions**, choose **Select**\. This tutorial grants only the `SELECT` permission; your requirements may vary\.  
![\[Granting table and column-level permissions to an Okta user.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-31.png)

1. Choose **Grant**\.

Now you perform similar steps for the Okta group\.

**To grant permissions in Lake Formation for the Okta group**

1. On the **Tables** page of the Lake Formation console, make sure that the **nyctaxi** table is still selected\.

1. From **Actions**, choose **Grant**\.

1. In the **Grant permissions** dialog, enter the following information:

   1. Under **SAML and Amazon QuickSight users and groups**, enter the Okta SAML group ARN in the following format:

      ```
      arn:aws:iam::<account-id>:saml-provider/AthenaLakeFormationOkta:group/lf-business-analyst
      ```

   1. For **Columns**, **Choose filter type**, choose **Include columns**\.

   1. For **Choose one or more columns**, choose the first three columns of the table\.

   1. For **Table permissions**, choose the specific access permissions to grant\. This tutorial grants only the `SELECT` permission; your requirements may vary\.  
![\[Granting table permissions to an Okta group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-31b.png)

1. Choose **Grant**\.

1. To verify the permissions that you granted, choose **Actions**, **View permissions**\.  
![\[Choose View permissions to verify the permissions that were granted.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-32.png)

   The **Data permissions** page for the `nyctaxi` table shows the permissions for **athena\-okta\-user** and the **lf\-business\-analyst** group\.  
![\[Viewing the permissions that were granted to the Okta user and group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-33.png)

## Step 7: Verify access through the Athena JDBC client<a name="security-athena-lake-formation-jdbc-okta-tutorial-step-7-verify-access-through-athena-jdbc-client"></a>

Now you are ready to use a JDBC client to perform a test connection to Athena as the Okta SAML user\. 

In this section, you perform the following tasks:
+ Prepare the test client – Download the Athena JDBC driver, install SQL Workbench, and add the driver to Workbench\. This tutorial uses SQL Workbench to access Athena through Okta authentication and to verify Lake Formation permissions\.
+ In SQL Workbench:
  + Create a connection for the Athena Okta user\.
  + Run test queries as the Athena Okta user\.
  + Create and test a connection for the business analyst user\.
+ In the Okta console, add the business analyst user to the developer group\.
+ In the Lake Formation console, configure table permissions for the developer group\.
+ In SQL Workbench, run test queries as the business analyst user and verify how the change in permissions affects the results\.

**To prepare the test client**

1. Download and extract the Lake Formation compatible Athena JDBC driver \(2\.0\.14 or later version\) from [Using Athena with the JDBC Driver](connect-with-jdbc.md)\.

1. Download and install the free [SQL Workbench/J](https://www.sql-workbench.eu/index.html) SQL query tool, available under a modified Apache 2\.0 license\.

1. In SQL Workbench, choose **File**, and then choose **Manage Drivers**\.  
![\[Choose Manage Drivers.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-1.png)

1. In the **Manage Drivers** dialog box, perform the following steps:

   1. Choose the new driver icon\.

   1. For **Name**, enter **Athena**\.

   1. For **Library**, browse to and choose the Simba Athena JDBC `.jar` file that you just downloaded\.

   1. Choose **OK**\.  
![\[Adding the Athena JDBC driver to SQL Workbench.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-2.png)

You are now ready to create and test a connection for the Athena Okta user\.

**To create a connection for the Okta user**

1. Choose **File**, **Connect window**\.  
![\[Choose Connect window.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-3.png)

1. In the **Connection profile** dialog box, create a connection by entering the following information:
   + In the name box, enter **Athena\_Okta\_User\_Connection**\.
   + For **Driver**, choose the Simba Athena JDBC Driver\.
   + For **URL**, do one of the following:
     + To use a connection URL, enter a single\-line connection string\. The following example adds line breaks for readability\.

       ```
       jdbc:awsathena://AwsRegion=region-id;
       S3OutputLocation=s3://athena-query-results-bucket/athena_results;
       AwsCredentialsProviderClass=com.simba.athena.iamsupport.plugin.OktaCredentialsProvider;
       user=athena-okta-user@anycompany.com;
       password=password;
       idp_host=okta-idp-domain;
       App_ID=okta-app-id;
       SSL_Insecure=true;
       LakeFormationEnabled=true;
       ```
     + To use an AWS profile\-based URL, perform the following steps:

       1. Configure an [AWS profile](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html) that has an AWS credentials file like the following example\.

          ```
          [athena_lf_dev]
          plugin_name=com.simba.athena.iamsupport.plugin.OktaCredentialsProvider
          idp_host=okta-idp-domain
          app_id=okta-app-id
          uid=athena-okta-user@anycompany.com
          pwd=password
          ```

       1. For **URL**, enter a single\-line connection string like the following example\. The example adds line breaks for readability\.

          ```
          jdbc:awsathena://AwsRegion=region-id;
          S3OutputLocation=s3://athena-query-results-bucket/athena_results;
          profile=athena_lf_dev;
          SSL_Insecure=true;
          LakeFormationEnabled=true;
          ```

     Note that these examples are basic representations of the URL needed to connect to Athena\. For the full list of parameters supported in the URL, refer to the [Simba Athena JDBC driver installation guide](connect-with-jdbc.md#documentation-jdbc)\. The JDBC installation guide also provides sample Java code for connecting to Athena programmatically\.

   The following image shows a SQL Workbench connection profile that uses a connection URL\.  
![\[A connection profile in SQL Workbench.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-4.png)

Now that you have established a connection for the Okta user, you can test it by retrieving some data\.

**To test the connection for the Okta user**

1. Choose **Test**, and then verify that the connection succeeds\.

1. From the SQL Workbench **Statement** window, run the following SQL `DESCRIBE` command\. Verify that all columns are displayed\.

   ```
   DESCRIBE "tripdb"."nyctaxi"
   ```  
![\[All columns displayed.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-5.png)

1. From the SQL Workbench **Statement** window, run the following SQL `SELECT` command\. Verify that all columns are displayed\.

   ```
   SELECT * FROM tripdb.nyctaxi LIMIT 5
   ```  
![\[Verify that all columns are displayed.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-6.png)

Next, you verify that the **athena\-ba\-user**, as a member of the **lf\-business\-analyst** group, has access to only the first three columns of the table that you specified earlier in Lake Formation\.

**To verify access for the **athena\-ba\-user****

1. In SQL Workbench, in the **Connection profile** dialog box, create another connection profile\.
   + For the connection profile name, enter ** Athena\_Okta\_Group\_Connection**\.
   + For **Driver**, choose the Simba Athena JDBC driver\.
   + For **URL**, do one of the following:
     + To use a connection URL, enter a single\-line connection string\. The following example adds line breaks for readability\.

       ```
       jdbc:awsathena://AwsRegion=region-id;
       S3OutputLocation=s3://athena-query-results-bucket/athena_results;
       AwsCredentialsProviderClass=com.simba.athena.iamsupport.plugin.OktaCredentialsProvider;
       user=athena-ba-user@anycompany.com;
       password=password;
       idp_host=okta-idp-domain;
       App_ID=okta-application-id;
       SSL_Insecure=true;
       LakeFormationEnabled=true;
       ```
     + To use an AWS profile\-based URL, perform the following steps:

       1. Configure an AWS profile that has a credentials file like the following example\.

          ```
          [athena_lf_ba]
          plugin_name=com.simba.athena.iamsupport.plugin.OktaCredentialsProvider
          idp_host=okta-idp-domain
          app_id=okta-application-id
          uid=athena-ba-user@anycompany.com
          pwd=password
          ```

       1. For **URL**, enter a single\-line connection string like the following\. The example adds line breaks for readability\.

          ```
          jdbc:awsathena://AwsRegion=region-id;
          S3OutputLocation=s3://athena-query-results-bucket/athena_results;
          profile=athena_lf_ba;
          SSL_Insecure=true;
          LakeFormationEnabled=true;
          ```

1. Choose **Test** to confirm that the connection is successful\.

1. From the **SQL Statement** window, run the same `DESCRIBE` and `SELECT` SQL commands that you did before and examine the results\.

   Because **athena\-ba\-user** is a member of the **lf\-business\-analyst** group, only the first three columns that you specified in the Lake Formation console are returned\.  
![\[Only the first three columns are returned.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-7.png)  
![\[Data from the first three columns.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-8.png)

Next, you return to the Okta console to add the `athena-ba-user` to the `lf-developer` Okta group\.

**To add the athena\-ba\-user to the lf\-developer group**

1. Sign in to the Okta console as an administrative user of the assigned Okta domain\.

1. Switch to the **Classic UI**\.

1. Choose **Directory**, and then choose **Groups**\.

1. On the Groups page, choose the **lf\-developer** group\.  
![\[Choose the lf-developer group.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-9.png)

1. Choose **Manage People**\.

1. From the **Not Members** list, choose the **athena\-ba\-user** to add it to the **lf\-developer group**\.

1. Choose **Save**\.

Now you return to the Lake Formation console to configure table permissions for the **lf\-developer** group\.

**To configure table permissions for the lf\-developer\-group**

1. Log into the Lake Formation console as Data Lake administrator\.

1. In the navigation pane, choose **Tables**\.

1. Select the **nyctaxi** table\.

1. Choose **Actions**, **Grant**\.

1. In the **Grant Permissions** dialog, enter the following information:
   + For **SAML and Amazon QuickSight users and groups**, enter the Okta SAML lf\-developer group ARN in the following format:
   + For **Columns**, **Choose filter type**, choose **Include columns**\.
   + Choose the **trip\_type** column\.
   + For **Table permissions**, choose **SELECT**\.

1. Choose **Grant**\.

Now you can use SQL Workbench to verify the change in permissions for the **lf\-developer** group\. The change should be reflected in the data available to **athena\-ba\-user**, who is now a member of the **lf\-developer** group\.

**To verify the change in permissions for athena\-ba\-user**

1. Close the SQL Workbench program, and then re\-open it\.

1. Connect to the profile for **athena\-ba\-user**\.

1. From the **Statement** window, issue the same SQL statements that you ran previously:

   This time, the **trip\_type** column is displayed\.  
![\[The fourth column is available for query.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-10.png)

   Because **athena\-ba\-user** is now a member of both the **lf\-developer** and **lf\-business\-analyst** groups, the combination of Lake Formation permissions for those groups determines the columns that are returned\.  
![\[The fourth column in the data results.\]](http://docs.aws.amazon.com/athena/latest/ug/images/security-athena-lake-formation-jdbc-okta-tutorial-verify-access-11.png)

## Conclusion<a name="security-athena-lake-formation-jdbc-okta-tutorial-conclusion"></a>

In this tutorial you configured Athena integration with AWS Lake Formation using Okta as the SAML provider\. You used Lake Formation and IAM to control the resources that are available to the SAML user in your data lake AWS Glue Data Catalog\.

## Related Resources<a name="security-athena-lake-formation-jdbc-okta-tutorial-related-resources"></a>

For related information, see the following resources\.
+ [Using Athena with the JDBC Driver](connect-with-jdbc.md)
+ [Enabling Federated Access to the Athena API](access-federation-saml.md)
+ [AWS Lake Formation Developer Guide](https://docs.aws.amazon.com/lake-formation/latest/dg/)
+ [Granting and Revoking Data Catalog Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/granting-catalog-permissions.html) in the *AWS Lake Formation Developer Guide*\.
+ [Identity Providers and Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html) in the *IAM User Guide*\.
+ [Creating IAM SAML Identity Providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml.html) in the *IAM User Guide*\.
+ [Enabling Federation to AWS Using Windows Active Directory, ADFS, and SAML 2\.0](http://aws.amazon.com/blogs/security/enabling-federation-to-aws-using-windows-active-directory-adfs-and-saml-2-0/) on the *AWS Security Blog*\.