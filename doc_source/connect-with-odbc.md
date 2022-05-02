# Connecting to Amazon Athena with ODBC<a name="connect-with-odbc"></a>

Download the Amazon Athena ODBC driver License Agreement, ODBC drivers, and ODBC documentation using the following links\. For information about the ODBC connection string, see the ODBC Driver Installation and Configuration Guide PDF file, downloadable from this page\. For permissions information, see [Access through JDBC and ODBC connections](policy-actions.md)\.

## Amazon Athena ODBC driver license agreement<a name="atelong-odbc-driver-license-agreement"></a>

 [License agreement](https://s3.amazonaws.com/athena-downloads/agreement/ODBC/Amazon+Athena+ODBC+Driver+License+Agreement.pdf) 

## ODBC driver download links<a name="connect-with-odbc-driver-and-documentation-download-links"></a>

### Windows<a name="connect-with-odbc-windows"></a>


| Driver version | Download link | 
| --- | --- | 
| ODBC 1\.1\.16 for Windows 32\-bit | [Windows 32 bit ODBC driver 1\.1\.16](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.16.1000/Windows/SimbaAthena_1.1.16.1000_32-bit.msi) | 
| ODBC 1\.1\.16 for Windows 64\-bit | [Windows 64 bit ODBC driver 1\.1\.16](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.16.1000/Windows/SimbaAthena_1.1.16.1000_64-bit.msi) | 

### Linux<a name="connect-with-odbc-linux"></a>


| Driver version | Download link | 
| --- | --- | 
| ODBC 1\.1\.16 for Linux 32\-bit | [Linux 32 bit ODBC driver 1\.1\.16](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.16.1000/Linux/simbaathena-1.1.16.1000-1.el7.i686.rpm) | 
| ODBC 1\.1\.16 for Linux 64\-bit | [Linux 64 bit ODBC driver 1\.1\.16](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.16.1000/Linux/simbaathena-1.1.16.1000-1.el7.x86_64.rpm) | 

### OSX<a name="connect-with-odbc-osx"></a>


| Driver version | Download link | 
| --- | --- | 
| ODBC 1\.1\.16 for OSX | [OSX ODBC driver 1\.1\.16](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.16.1000/OSX/SimbaAthena_1.1.16.1000.dmg) | 

### Documentation<a name="connect-with-odbc-driver-documentation"></a>


| Driver version | Download link | 
| --- | --- | 
| Documentation for ODBC 1\.1\.16 | [ODBC driver installation and configuration guide version 1\.1\.16](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.16.1000/docs/Simba+Amazon+Athena+ODBC+Connector+Install+and+Configuration+Guide.pdf) | 
| Release Notes for ODBC 1\.1\.16 | [ODBC driver release notes version 1\.1\.16](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.1.16.1000/docs/release-notes.txt) | 

## Migration from the previous version of the ODBC driver<a name="migration-from-previous-odbc-driver"></a>

The current ODBC driver version 1\.1\.16 is a drop\-in replacement of the previous versions of the ODBC driver\. It is also backward compatible with the ODBC driver version 1\.0\.3, if you use the following required steps to make sure that the driver runs\. 

**Important**  
To use the ODBC driver versions 1\.0\.3 and greater, follow these requirements:   
Keep port 444, which Athena uses to stream query results, open to outbound traffic\. When you use a PrivateLink endpoint to connect to Athena, ensure that the security group attached to the PrivateLink endpoint is open to inbound traffic on port 444\. 
Add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API operation, and is used only with the ODBC and JDBC drivers, as part of streaming results support\. For an example policy, see [AWSQuicksightAthenaAccess Managed Policy](managed-policies.md#awsquicksightathenaaccess-managed-policy)\. 

## Previous versions of the ODBC driver<a name="odbc-previous-versions"></a>


| Driver version 1\.0\.5 | Download link | 
| --- | --- | 
| ODBC 1\.0\.5 for Windows 32\-bit | [Windows 32 bit ODBC driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.5 for Windows 64\-bit | [Windows 64 bit ODBC driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.5 for Linux 32\-bit | [Linux 32 bit ODBC driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Linux/simbaathena-1.0.5.1006-1.i686.rpm) | 
| ODBC 1\.0\.5 for Linux 64\-bit | [Linux 64 bit ODBC driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Linux/simbaathena-1.0.5.1006-1.x86_64.rpm) | 
| ODBC 1\.0\.5 for OSX | [OSX ODBC driver 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.5 | [ODBC driver installation and configuration guide version 1\.0\.5](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.5/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf)  | 


| Driver version 1\.0\.4 | Download link | 
| --- | --- | 
| ODBC 1\.0\.4 for Windows 32\-bit | [Windows 32 bit ODBC driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.4 for Windows 64\-bit | [Windows 64 bit ODBC driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.4 for Linux 32\-bit | [Linux 32 bit ODBC driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Linux/simbaathena-1.0.4.1005-1.i686.rpm) | 
| ODBC 1\.0\.4 for Linux 64\-bit | [Linux 64 bit ODBC driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Linux/simbaathena-1.0.4.1005-1.x86_64.rpm) | 
| ODBC 1\.0\.4 for OSX | [OSX ODBC driver 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.4 | [ODBC driver installation and configuration guide version 1\.0\.4](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.4/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf)  | 


| Driver version 1\.0\.3 | Download link | 
| --- | --- | 
| ODBC 1\.0\.3 for Windows 32\-bit | [Windows 32\-bit ODBC driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.3 for Windows 64\-bit | [Windows 64\-bit ODBC driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.3 for Linux 32\-bit | [Linux 32\-bit ODBC driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Linux/simbaathena-1.0.3.1004-1.i686.rpm) | 
| ODBC 1\.0\.3 for Linux 64\-bit | [Linux 64\-bit ODBC driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Linux/simbaathena-1.0.3.1004-1.x86_64.rpm) | 
| ODBC 1\.0\.3 for OSX | [OSX ODBC driver 1\.0](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.3 | [ODBC driver installation and configuration guide version 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf) | 


| Driver version 1\.0\.2 | Download link | 
| --- | --- | 
| ODBC 1\.0\.2 for Windows 32\-bit | [Windows 32\-bit ODBC driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.2 for Windows 64\-bit | [Windows 64\-bit ODBC driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.2 for Linux 32\-bit | [Linux 32\-bit ODBC driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Linux/simbaathena-1.0.2.1003-1.i686.rpm) | 
| ODBC 1\.0\.2 for Linux 64\-bit | [Linux 64\-bit ODBC driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Linux/simbaathena-1.0.2.1003-1.x86_64.rpm) | 
| ODBC 1\.0 for OSX | [OSX ODBC driver 1\.0](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.2 | [ODBC driver installation and configuration guide version 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf) | 

## ODBC driver notes<a name="connect-with-odbc-configuration"></a>

**Connecting Without Using a Proxy**  
If you want to specify certain hosts that the driver connects to without using a proxy, you can use the optional `NonProxyHost` property in your ODBC connection string\.

The `NonProxyHost` property specifies a comma\-separated list of hosts that the connector can access without going through the proxy server when a proxy connection is enabled, as in the following example:

```
.amazonaws.com,localhost,.example.net,.example.com
```

The `NonProxyHost` connection parameter is passed to the `CURLOPT_NOPROXY` curl option\. For information about the `CURLOPT_NOPROXY` format, see [CURLOPT\_NOPROXY](https://curl.se/libcurl/c/CURLOPT_NOPROXY.html) in the curl documentation\. 