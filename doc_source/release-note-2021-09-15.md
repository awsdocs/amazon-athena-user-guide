# September 15, 2021<a name="release-note-2021-09-15"></a>

Published on 2021\-09\-15

You can now use a redesigned Amazon Athena console \(Preview\)\. A new Athena JDBC driver has been released\.

## Athena Console Preview<a name="release-note-2021-09-15-console-preview"></a>

You can now use a redesigned [Amazon Athena](http://aws.amazon.com/athena/) console \(Preview\) from any AWS Region where Athena is available\. The new console supports all of the features of the existing console, but from an easier to use, modern interface\.

To switch to the new [console](https://console.aws.amazon.com/athena/home), log into your AWS account and choose Amazon Athena\. From the AWS console navigation bar, choose **Switch to the new console**\. To return to the default console, deselect **New Athena experience** from the navigation panel on the left\.

Get started with the new [console](https://console.aws.amazon.com/athena/home) today\. Choose **Feedback** in the bottom\-left corner to tell us about your experience\.

## Athena JDBC Driver 2\.0\.24<a name="release-note-2021-09-15-jdbc-2-0-24"></a>

Athena announces availability of JDBC driver version 2\.0\.24 for Athena\. This release updates proxy support for all credentials providers\. The driver now supports proxy authentication for all hosts that are not supported by the `NonProxyHosts` connection property\. 

As a convenience, this release includes downloads of the JDBC driver both with and without the AWS SDK\. This JDBC driver version allows you to have both the AWS\-SDK and the Athena JDBC driver embedded in project\.

For more information and to download the new driver, release notes, and documentation, see [Using Athena with the JDBC Driver](connect-with-jdbc.md)\.