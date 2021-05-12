# Using Lake Formation and the Athena JDBC and ODBC Drivers for Federated Access to Athena<a name="security-athena-lake-formation-jdbc"></a>

The Athena JDBC and ODBC drivers support SAML 2\.0\-based federation with Athena using Okta and Microsoft Active Directory Federation Services \(AD FS\) identity providers\. By integrating Amazon Athena with AWS Lake Formation, you enable SAML\-based authentication to Athena with corporate credentials\. With Lake Formation and AWS Identity and Access Management \(IAM\), you can maintain fine\-grained, column\-level access control over the data available to the SAML user\. With the Athena JDBC and ODBC drivers, federated access is available for tool or programmatic access\.

To use Athena to access a data source controlled by Lake Formation, you need to enable SAML 2\.0\-based federation by configuring your identity provider \(IdP\) and AWS Identity and Access Management \(IAM\) roles\. For detailed steps, see [Tutorial: Configuring Federated Access for Okta Users to Athena Using Lake Formation and JDBC](security-athena-lake-formation-jdbc-okta-tutorial.md)\.

## Prerequisites<a name="security-athena-lake-formation-jdbc-prerequisites"></a>

To use Amazon Athena and Lake Formation for federated access, you must meet the following requirements:
+ You manage your corporate identities using an existing SAML\-based identity provider, such as Okta or Microsoft Active Directory Federation Services \(AD FS\)\.
+ You use the AWS Glue Data Catalog as a metadata store\.
+ You define and manage permissions in Lake Formation to access databases, tables, and columns in AWS Glue Data Catalog\. For more information, see the [AWS Lake Formation Developer Guide](https://docs.aws.amazon.com/lake-formation/latest/dg/)\.
+ You use version 2\.0\.14 or later of the [Athena JDBC Driver](https://docs.aws.amazon.com/athena/latest/ug/connect-with-jdbc.html) or version 1\.1\.3 or later of the [Athena ODBC driver](connect-with-odbc.md)\.

## Considerations and Limitations<a name="security-athena-lake-formation-jdbc-considerations-and-limitations"></a>

When using the Athena JDBC or ODBC driver and Lake Formation to configure federated access to Athena, keep in mind the following points:
+ Currently, the Athena JDBC driver and ODBC drivers support the Okta and Microsoft Active Directory Federation Services \(AD FS\) identity providers\. Although the Athena JDBC driver has a generic SAML class that can be extended to use other identity providers, support for custom extensions that enable other identity providers \(IdPs\) for use with Athena may be limited\.
+ Currently, you cannot use the Athena console to configure support for IdP and SAML use with Athena\. To configure this support, you use the third\-party identity provider, the Lake Formation and IAM management consoles, and the JDBC or ODBC driver client\.
+ You should understand the [SAML 2\.0 specification](https://www.oasis-open.org/standards#samlv2.0) and how it works with your identity provider before you configure your identity provider and SAML for use with Lake Formation and Athena\.
+ SAML providers and the Athena JDBC and ODBC drivers are provided by third parties, so support through AWS for issues related to their use may be limited\.

**Topics**
+ [Prerequisites](#security-athena-lake-formation-jdbc-prerequisites)
+ [Considerations and Limitations](#security-athena-lake-formation-jdbc-considerations-and-limitations)
+ [Tutorial: Configuring Federated Access for Okta Users to Athena Using Lake Formation and JDBC](security-athena-lake-formation-jdbc-okta-tutorial.md)