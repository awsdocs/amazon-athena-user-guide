# Connecting to Amazon Athena with ODBC<a name="connect-with-odbc"></a>

Download the ODBC driver, the Amazon Athena ODBC driver License Agreement, and the documentation for the driver using the following links\.

## Amazon Athena ODBC Driver License Agreement<a name="atelong-odbc-driver-license-agreement"></a>

 [License Agreement](https://s3.amazonaws.com/athena-downloads/agreement/ODBC/Amazon+Athena+ODBC+Driver+License+Agreement.pdf) 

## Windows<a name="windows"></a>


| Driver Version | Download Link | 
| --- | --- | 
| ODBC 1\.0\.3 for Windows 32\-bit | [Windows 32 bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.3 for Windows 64\-bit | [Windows 64 bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Windows/Simba+Athena+1.0+64-bit.msi) | 

## Linux<a name="linux"></a>


| Driver Version | Download Link | 
| --- | --- | 
| ODBC 1\.0\.3 for Linux 32\-bit | [Linux 32 bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Linux/simbaathena-1.0.3.1004-1.i686.rpm) | 
| ODBC 1\.0\.3 for Linux 64\-bit | [Linux 64 bit ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Linux/simbaathena-1.0.3.1004-1.x86_64.rpm) | 

## OSX<a name="osx"></a>


| Driver Version | Download Link | 
| --- | --- | 
| ODBC 1\.0\.3 for OSX | [OSX ODBC Driver 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/OSX/Simba+Athena+1.0.dmg) | 

## ODBC Driver Documentation<a name="odbc-driver-documentation"></a>


| Driver Version | Download Link | 
| --- | --- | 
| Documentation for ODBC 1\.0\.3 | [ODBC Driver Installation and Configuration Guide version 1\.0\.3](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/SimbaAthenaODBC_1.0.3/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf) | 

## Migration from the Previous Version of the ODBC Driver<a name="migration-from-previous-odbc-driver"></a>

The current ODBC driver version 1\.0\.3 is a drop\-in replacement of the previous version of the ODBC driver version 1\.0\.2, and is backwards compatible with the ODBC driver version 1\.0\.2, with the following steps that you must perform to ensure the driver runs\. 

**Important**  
To use the ODBC driver version 1\.0\.3, follow these requirements:   
Keep the port 444 open to outbound traffic\.
Add the `athena:GetQueryResultsStream` policy action to the list of policies for Athena\. This policy action is not exposed directly with the API and is only used with the ODBC and JDBC drivers, as part of streaming results support\. For an example policy, see [AWSQuicksightAthenaAccess Managed Policy](managed-policies.md#awsquicksightathenaaccess-managed-policy)\. 

## Previous Versions of the ODBC Driver<a name="odbc-previous-versions"></a>


| Driver Version | Download Link | 
| --- | --- | 
| ODBC 1\.0\.2 for Windows 32\-bit | [Windows 32\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Windows/Simba+Athena+1.0+32-bit.msi) | 
| ODBC 1\.0\.2 for Windows 64\-bit | [Windows 64\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Windows/Simba+Athena+1.0+64-bit.msi) | 
| ODBC 1\.0\.2 for Linux 32\-bit | [Linux 32\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Linux/simbaathena-1.0.2.1003-1.i686.rpm) | 
| ODBC 1\.0\.2 for Linux 64\-bit | [Linux 64\-bit ODBC Driver 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Linux/simbaathena-1.0.2.1003-1.x86_64.rpm) | 
| ODBC 1\.0 for OSX | [OSX ODBC Driver 1\.0](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/OSX/Simba+Athena+1.0.dmg) | 
| Documentation for ODBC 1\.0\.2 | [ODBC Driver Installation and Configuration Guide version 1\.0\.2](https://s3.amazonaws.com/athena-downloads/drivers/ODBC/Simba+Athena+ODBC+Install+and+Configuration+Guide.pdf) | 