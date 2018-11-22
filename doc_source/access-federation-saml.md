# Enabling Federated Access to Athena API<a name="access-federation-saml"></a>

This section discusses federated access that allows a user or client application in your organization to call Athena API operations\. In this case, your organization's users don't have direct access to Athena\. Instead, you manage user credentials outside of AWS in Microsoft Active Directory\. Active Directory supports [SAML 2\.0](https://wiki.oasis-open.org/security) \(Security Assertion Markup Language 2\.0\)\.

In this case, to authenticate users, use the JDBC or ODBC driver with SAML\.2\.0 support that accesses Active Directory Federation Services \(AD FS\) 3\.0 and enables a client application to call Athena API operations\.

For more information about SAML 2\.0 support in AWS, see [About SAML 2\.0 Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html) in the *IAM User Guide*\. 

**Note**  
Federated access to Athena API is supported for a particular type of identity provider \(IdP\), the Active Directory Federation Service \(AD FS 3\.0\), which is part of Windows Server\. Access is established through the versions of JDBC or ODBC drivers that support SAML 2\.0\. For information, see [Using Athena with the JDBC Driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

**Topics**
+ [Before You Begin](#access-federation-before-you-begin)
+ [Architecture Diagram](#access-federation-diagram)
+ [Procedure: SAML\-based Federated Access to Athena API](#access-federation-procedure)

## Before You Begin<a name="access-federation-before-you-begin"></a>

 Before you begin, complete the following prerequisites: 
+ Inside your organization, install and configure the AD FS 3\.0 as your IdP\.
+ On the Athena side, install and configure the latest available versions of JDBC or ODBC drivers that include support for federated access compatible with SAML 2\.0\. For information, see [Using Athena with the JDBC Driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

## Architecture Diagram<a name="access-federation-diagram"></a>

The following diagram illustrates this process\.

![\[The diagram of the federated access to Athena API.\]](http://docs.aws.amazon.com/athena/latest/ug/images/athena-saml-based-federation.png)

In this diagram:

1. A user in your organization uses a client application with the JDBC or ODBC driver to request authentication from your organization's IdP\. The IdP is AD FS 3\.0\.

1. The IdP authenticates the user against Active Directory, which is your organization's Identity Store\.

1. The IdP constructs a SAML assertion with information about the user and sends the assertion to the client application via the JDBC or ODBC driver\.

1. The JDBC or ODBC driver calls the AWS Security Token Service [AssumeRoleWithSAML](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithSAML.html) API operation, passing it the following parameters:
   + The ARN of the SAML provider
   + The ARN of the role to assume
   + The SAML assertion from the IdP

   For more information, see [AssumeRoleWithSAML](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithSAML.html), in the *AWS Security Token Service API Reference*\.

1. The API response to the client application via the JDBC or ODBC driver includes temporary security credentials\.

1. The client application uses the temporary security credentials to call Athena API operations, allowing your users to access Athena API operations\.

## Procedure: SAML\-based Federated Access to Athena API<a name="access-federation-procedure"></a>

In this procedure, actions take place either on the side of your organization, or within AWS\. The procedure provides steps that establish trust between your organization's IdP and your AWS account to enable SAML\-based federated access to the AWS Athena API operation\.

**To enable federated access to the Athena API:**

1. In your organization, register AWS as a service provider \(SP\) in your IdP\. This process is known as *relying party trust*\. For more information, see [Configuring your SAML 2\.0 IdP with Relying Party Trust](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml_relying-party.html) in the *IAM User Guide*\. As part of this task, perform these steps:

   1. Obtain the sample SAML metadata document from this URL: [https://signin.aws.amazon.com/static/saml-%20metadata.xml](https://signin.aws.amazon.com/static/saml-%20metadata.xml)\.

   1. In your organization's IdP \(AD FS\), generate an equivalent metadata XML file that describes your IdP as an identity provider to AWS\. Your metadata file must include the issuer name, creation date, expiration date, and keys that AWS uses to validate authentication responses \(assertions\) from your organization\. 

1. In the IAM console, create a SAML identity provider entity\. For more information, see [Creating SAML Identity Providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml.html) in the *IAM User Guide*\. As part of this step, do the following: 

   1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

   1. Upload the SAML metadata document produced by the IdP \(AD FS\) in Step 1 in this procedure\. 

1. In the IAM console, create one or more IAM roles for your IdP\. For more information, see [Creating a Role for a Third\-Party Identity Provider \(Federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\. As part of this step, do the following: 
   + In the role's permission policy, list actions that users from your organization are allowed to do in AWS\. 
   + In the role's trust policy, set the SAML provider entity that you created in Step 2 of this procedure as the principal\. 

   This establishes a trust relationship between your organization and AWS\.

1. In your organization's IdP \(AD FS\), define assertions that map users or groups in your organization to the IAM roles\. The mapping of users and groups to the IAM roles is also known as a *claim rule*\. Note that different users and groups in your organization might map to different IAM roles\. 

   For information about configuring the mapping in AD FS, see the blog post: [Enabling Federation to AWS Using Windows Active Directory, ADFS, and SAML 2\.0](https://aws.amazon.com/blogs/security/enabling-federation-to-aws-using-windows-active-directory-adfs-and-saml-2-0/)\.

1. Install and configure the JDBC or ODBC driver with SAML 2\.0 support\. For information, see [Using Athena with the JDBC Driver](connect-with-jdbc.md) and [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\.

1. Specify the connection string from your application to the JDBC or ODBC driver\. For information about the connection string that your application should use, see the topic *"Using the Active Directory Federation Services \(ADFS\) Credentials Provider"* in the [ JDBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC_2.0.6/docs/Simba+Athena+JDBC+Driver+Install+and+Configuration+Guide.pdf), or a similar topic in the [ODBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf)\.

   The high\-level summary of configuring the connection string to the drivers is as follows:
   + In the `AwsCredentialsProviderClass configuration`, set the `com.simba.athena.iamsupport.plugin.AdfsCredentialsProvider` to indicate that you want to use SAML 2\.0 based authentication via AD FS IdP\. 
   + For `idp_host`, provide the host name of the AD FS IdP server\.
   + For `idp_port`, provide the port number that the AD FS IdP listens on for the SAML assertion request\.
   + For `UID` and `PWD`, provide the AD domain user credentials\. When using the driver on Windows, if `UID` and `PWD` are not provided, the driver attempts to obtain the user credentials of the user logged in to the Windows machine\.
   + Optionally, set `ssl_insecure` to 1 if the AD FS IdP’s SSL certificate has not been configured to be trusted by the driver\.
   + To enable mapping of an Active Directory domain user or group to one or more IAM roles \(as mentioned in step 4 of this procedure\), in the `preferred_role` for the JDBC or ODBC connection, specify the IAM role \(ARN\) to assume for the driver connection\. Specifying the `preferred_role` is optional, and is useful if the role is not the first role listed in the claim rule\.

   As a result, the following actions occur:

   1. The JDBC or ODBC driver calls the AWS STS [AssumeRoleWithSAML](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithSAML.html) API, and passes it the assertions, as shown in step 4 of the [architecture diagram](#access-federation-diagram)\. 

   1. AWS makes sure that the request to assume the role comes from the IdP referenced in the SAML provider entity\. 

   1. If the request is successful, the AWS STS [AssumeRoleWithSAML](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithSAML.html) API operation returns a set of temporary security credentials, which your client application uses to make signed requests to Athena\. 

      Your application now has information about the current user and can access Athena programmatically\. 