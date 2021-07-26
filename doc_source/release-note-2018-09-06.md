# September 6, 2018<a name="release-note-2018-09-06"></a>

Published on *2018\-09\-06*

Released the new version of the ODBC driver \(version 1\.0\.3\)\. The new version of the ODBC driver streams results by default, instead of paging through them, allowing business intelligence tools to retrieve large data sets faster\. This version also includes improvements, bug fixes, and an updated documentation for *"Using SSL with a Proxy Server"*\. For details, see the [Release Notes](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/release-notes.txt) for the driver\.

For downloading the ODBC driver version 1\.0\.3 and its documentation, see [Connecting to Amazon Athena with ODBC](connect-with-odbc.md)\. 

The streaming results feature is available with this new version of the ODBC driver\. It is also available with the JDBC driver\. For information about streaming results, see the [ ODBC Driver Installation and Configuration Guide](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf), and search for **UseResultsetStreaming**\.

The ODBC driver version 1\.0\.3 is a drop\-in replacement for the previous version of the driver\. We recommend that you migrate to the current driver\. 

**Important**  
To use the ODBC driver version 1\.0\.3, follow these requirements:   
Keep the port 444 open to outbound traffic\.
Add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API and is only used with the ODBC and JDBC drivers, as part of streaming results support\. For an example policy, see [AWSQuicksightAthenaAccess Managed Policy](managed-policies.md#awsquicksightathenaaccess-managed-policy)\. 