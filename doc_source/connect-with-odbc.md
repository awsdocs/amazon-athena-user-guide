# Connecting to Amazon Athena with ODBC<a name="connect-with-odbc"></a>

Download the Amazon Athena ODBC driver License Agreement, ODBC drivers, and ODBC documentation using the following links\. For permissions information, see [Access through JDBC and ODBC Connections](policy-actions.md)\.

## Amazon Athena ODBC Driver License Agreement<a name="atelong-odbc-driver-license-agreement"></a>

 [License Agreement](https://s3.amazonaws.com/athena-downloads/agreement/ODBC/Amazon+Athena+ODBC+Driver+License+Agreement.pdf) 

## ODBC Driver Download Links<a name="connect-with-odbc-driver-and-documentation-download-links"></a>

### Windows<a name="connect-with-odbc-windows"></a>


| Driver Version | Download Link | 
| --- | --- | 
| ODBC 1\.1\.13 for Windows 32\-bit | [Windows 32 bit ODBC Driver 1\.1\.13](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.13.1000/Windows/Simba+Athena+1.1.13_32-bit.msi) | 
| ODBC 1\.1\.13 for Windows 64\-bit | [Windows 64 bit ODBC Driver 1\.1\.13](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.13.1000/Windows/Simba+Athena+1.1.13_64-bit.msi) | 

### Linux<a name="connect-with-odbc-linux"></a>


| Driver Version | Download Link | 
| --- | --- | 
| ODBC 1\.1\.13 for Linux 32\-bit | [Linux 32 bit ODBC Driver 1\.1\.13](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.13.1000/Linux/simbaathena-1.1.13.1000-1.el7.i686.rpm) | 
| ODBC 1\.1\.13 for Linux 64\-bit | [Linux 64 bit ODBC Driver 1\.1\.13](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.13.1000/Linux/simbaathena-1.1.13.1000-1.el7.x86_64.rpm) | 

### OSX<a name="connect-with-odbc-osx"></a>


| Driver Version | Download Link | 
| --- | --- | 
| ODBC 1\.1\.13 for OSX | [OSX ODBC Driver 1\.1\.13](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.13.1000/OSX/SimbaAthena1.1.13.1000.dmg) | 

### Documentation<a name="connect-with-odbc-driver-documentation"></a>


| Driver Version | Download Link | 
| --- | --- | 
| Documentation for ODBC 1\.1\.13 | [ODBC Driver Installation and Configuration Guide version 1\.1\.13](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.13.1000/docs/Simba+Athena+ODBC+Connector+Install+and+Configuration+Guide.pdf) | 
| Release Notes for ODBC 1\.1\.13 | [ODBC Driver Release Notes version 1\.1\.13](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.13.1000/docs/release-notes.txt) | 

## Migration from the Previous Version of the ODBC Driver<a name="migration-from-previous-odbc-driver"></a>

The current ODBC driver version 1\.1\.13 is a drop\-in replacement of the previous versions of the ODBC driver\. It is also backward compatible with the ODBC driver version 1\.0\.3, if you use the following required steps to make sure that the driver runs\. 

**Important**  
To use the ODBC driver versions 1\.0\.3 and greater, follow these requirements:   
Keep port 444, which Athena uses to stream query results, open to outbound traffic\. When you use a PrivateLink endpoint to connect to Athena, ensure that the security group attached to the PrivateLink endpoint is open to inbound traffic on port 444\. 
Add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API operation, and is used only with the ODBC and JDBC drivers, as part of streaming results support\. For an example policy, see [AWSQuicksightAthenaAccess Managed Policy](managed-policies.md#awsquicksightathenaaccess-managed-policy)\. 

## Previous Versions of the ODBC Driver<a name="odbc-previous-versions"></a>


| Driver Version 1\.0\.5 | Download Link | 
| --- | --- | 
| ODBC 1\.0\.5 for Windows 32\-bit | [Windows 32 bit ODBC Driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.5 for Windows 64\-bit | [Windows 64 bit ODBC Driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.5 for Linux 32\-bit | [Linux 32 bit ODBC Driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Linux/simbaathena-1.0.5.1006-1.i686.rpm) | 
| ODBC 1\.0\.5 for Linux 64\-bit | [Linux 64 bit ODBC Driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Linux/simbaathena-1.0.5.1006-1.x86_64.rpm) | 
| ODBC 1\.0\.5 for OSX | [OSX ODBC Driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.5 | [ODBC Driver Installation and Configuration Guide version 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf)  | 


| Driver Version 1\.0\.4 | Download Link | 
| --- | --- | 
| ODBC 1\.0\.4 for Windows 32\-bit | [Windows 32 bit ODBC Driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.4 for Windows 64\-bit | [Windows 64 bit ODBC Driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.4 for Linux 32\-bit | [Linux 32 bit ODBC Driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Linux/simbaathena-1.0.4.1005-1.i686.rpm) | 
| ODBC 1\.0\.4 for Linux 64\-bit | [Linux 64 bit ODBC Driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Linux/simbaathena-1.0.4.1005-1.x86_64.rpm) | 
| ODBC 1\.0\.4 for OSX | [OSX ODBC Driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.4 | [ODBC Driver Installation and Configuration Guide version 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf)  | 


| Driver Version 1\.0\.3 | Download Link | 
| --- | --- | 
| ODBC 1\.0\.3 for Windows 32\-bit | [Windows 32\-bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.3 for Windows 64\-bit | [Windows 64\-bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.3 for Linux 32\-bit | [Linux 32\-bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Linux/simbaathena-1.0.3.1004-1.i686.rpm) | 
| ODBC 1\.0\.3 for Linux 64\-bit | [Linux 64\-bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Linux/simbaathena-1.0.3.1004-1.x86_64.rpm) | 
| ODBC 1\.0\.3 for OSX | [OSX ODBC Driver 1\.0](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.3 | [ODBC Driver Installation and Configuration Guide version 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf) | 


| Driver Version 1\.0\.2 | Download Link | 
| --- | --- | 
| ODBC 1\.0\.2 for Windows 32\-bit | [Windows 32\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.2 for Windows 64\-bit | [Windows 64\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.2 for Linux 32\-bit | [Linux 32\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Linux/simbaathena-1.0.2.1003-1.i686.rpm) | 
| ODBC 1\.0\.2 for Linux 64\-bit | [Linux 64\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Linux/simbaathena-1.0.2.1003-1.x86_64.rpm) | 
| ODBC 1\.0 for OSX | [OSX ODBC Driver 1\.0](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.2 | [ODBC Driver Installation and Configuration Guide version 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf) | 

## ODBC Driver Notes<a name="connect-with-odbc-configuration"></a>

**Connecting Without Using a Proxy**  
If you want to specify certain hosts that the driver connects to without using a proxy, you can use the optional `NonProxyHost` property in your ODBC connection string\.

The `NonProxyHost` property specifies a comma\-separated list of hosts that the connector can access without going through the proxy server when a proxy connection is enabled, as in the following example:

```
.amazonaws.com,localhost,.example.net,.example.com
```

The `NonProxyHost` connection parameter is passed to the `CURLOPT_NOPROXY` curl option\. For information about the `CURLOPT_NOPROXY` format, see [CURLOPT\_NOPROXY](https://curl.se/libcurl/c/CURLOPT_NOPROXY.html) in the curl documentation\. 