# Encryption in Transit<a name="encryption-in-transit"></a>

In addition to encrypting data at rest in Amazon S3, Amazon Athena uses Transport Layer Security \(TLS\) encryption for data in\-transit between Athena and Amazon S3, and between Athena and customer applications accessing it\.

You should allow only encrypted connections over HTTPS \(TLS\) using the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_Boolean](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_Boolean) on Amazon S3 bucket IAM policies\.

Query results that stream to JDBC or ODBC clients are encrypted using TLS\. For information about the latest versions of the JDBC and ODBC drivers and their documentation, see [Connect with the JDBC Driver](connect-with-jdbc.md) and [Connect with the ODBC Driver](connect-with-odbc.md)\.